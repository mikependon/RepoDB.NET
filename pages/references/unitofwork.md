---
layout: default
sidebar: references
title: "Unit of Work"
nav_order: 11
permalink: /reference/unitofwork
tags: [repodb, class, unitofwork, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: References
---

# Unit of Work

---

This page contains the recommended way of implementing a Unit of Work (UOW) object when using this library. The consolidated output of this page can be found [here](/reference/output/unitofwork).

### Interface

Create an inteface that would enable your UOW class to be dependency injectable.

```csharp
public interface IUnitOfWork<TDbConnection>
{
    TDbConnection Connection { get; }
    DbTransaction Transaction { get ; }
    void Begin();
    void Rollback();
    void Commit();
}
```

Ensure that this interface is accepting a generic type of the connection object.

Then, add the following properties that will hold the state of transaction.

- `Connection` - will hold the instance of the connection object.
- `Transaction` - will hold the instance of the active transaction.

In addition to this, create the 3 basic transactional methods.

- `Begin` - is used to begin a new transaction.
- `Commit` - is used to commit an existing transaction.
- `Rollback` - is used to rollback an existing transaction.

### Class

Create a class that implements the newly created interface.

```csharp
public class RepoDbUnitOfWork : IUnitOfWork<SqlConnection>
{
    private AppSettings settings;
    private SqlConnection connection;
    private DbTransaction transaction;

    public RepoDbUnitOfWork(IOptions<AppSettings> settings)
    {
        this.settings = settings.Value;
    }

    public SqlConnection Connection { get { return connection; } }
    
    public DbTransaction Transaction { get { return transaction; } }
}
```
> Notice the private variable `transaction`. It is used to hold the instance of active transaction.

Create a `private` method that ensures a connection instance.

```csharp
private SqlConnection EnsureConnection() =>
    connection ?? connection = new SqlConnection(settings.ConnectionString);
```

Implement the interface method that starts a new transaction.

```csharp
public void Start()
{
    if (transaction != null)
    {
        throw new InvalidOperationException("Cannot start a new transaction while the existing other one is still open.");
    }
    var connection = EnsureConnection();
    transaction = connection.BeginTransaction();
}
```

Implement the interface method that commits the existing transaction.

```csharp
public void Commit()
{
    if (transaction == null)
    {
        throw new InvalidOperationException("There is no active transaction to commit.");
    }
    using (transaction)
    {
        transaction.Commit();
    }
    transaction = null;
}
```

Implement the interface method that rollbacks the existing transaction.

```csharp
public void Rollback()
{
    if (transaction == null)
    {
        throw new InvalidOperationException("There is no active transaction to rollback.");
    }
    using (transaction)
    {
        transaction.Rollback();
    }
    transaction = null;
}
```

> Please note that the corresponding asynchronous methods were not implemented on this sample. You have to introduce it yourself if you wish to have it covered.

### Repositories

First, implement the base interface that contains the necessary methods.

```csharp
public interface IRepository<TEntity> where TEntity : class
{
    void Attach(IUnitOfWork unitOfWork);
    TResult Add<TResult>(TEntity entity);
    int AddAll<TResult>(IEnumerable<TEntity> entities);
    int Delete(object id);
    int Delete(Entity entity);
    TResult Merge<TResult>(TEntity entity);
    TEntity Query(object id);
    int Update(TEntity entity);
}
```

> Take note of the method `Attach()`, it accepts an instance of `IUnitOfWork` object. This method will be used in the business logic to passed the currently injected (in-used) UOW class.

Then, implement the entity level interface repository.

```csharp
public interface IOrderRepository : IRepository<Order>
{
    ...
}
```

Then, create a repository class that inherits either the [DbRepository](/class/dbrepository) or [BaseRepository](/class/baserepository). On this class, implement the newly created `IOrderRepository` interface, the one that implements the `IRepository<TEntity>` interface.

```csharp
public class OrderRepository : BaseRepository<Order, SqlConnection>, IOrderRepository
{
    private UnitOfWork unitOfWork;

    public OrderRepository(IOptions<Settings> settings)
        : base(settings.Value.ConnectionString)
    { }
}
```

> Notice the variable `unitOfWork`. It will hold the instance of `IUnitOfWork` interface if the `Attach()` method is called.

Then, implement the method `Attach()`.

```csharp
public void Attach(IUnitOfWork unitOfWork)
{
    this.unitOfWork = unitOfWork;
}
```

Then, implement the methods of the interface. In each method, ensure to pass the instance of the `Transaction` object that is being held by the `IUnitOfWork` interface.

#### Add

```csharp
public TResult Add<TResult>(Order entity)
{
    return Insert<Order, TResult>(entity,
        transaction: unitOfWork?.Transaction);
}
```

#### AddAll

```csharp
public int AddAll(IEnumerable<Order> entities)
{
    return InsertAll<Order>(entities,
        transaction: unitOfWork?.Transaction);
}
```

#### Delete

```csharp
public int Delete(object id)
{
    return Delete<Order>(id,
        transaction: unitOfWork?.Transaction);
}

public int Delete(Order entity)
{
    return Delete<Order>(entity,
        transaction: unitOfWork?.Transaction);
}
```

#### Merge

```csharp
public TResult Merge<TResult>(Order entity)
{
    return Merge<Order, TResult>(entity,
        transaction: unitOfWork?.Transaction);
}
```

#### Query

```csharp
public TResult Query(object id)
{
    return Query<Order>(id,
        transaction: unitOfWork?.Transaction);
}
```

#### Update

```csharp
public int Update(Order entity)
{
    return Update<Order>(entity,
        transaction: unitOfWork?.Transaction);
}
```

Then, implement the same strategy when implementing the other repositories. Let us assumed, you also had created the corresponding repository for the `OrderItem` entity model.

- `OrderItemRepository` - the repository for the `OrderItem`.

> Please note that the corresponding asynchronous methods were not implemented on this sample. You have to introduce it yourself if you wish to have it covered.

### Dependency Injection

Register the UOW interface and class via service registration. Ensure it is on transient level.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IUnitOfWork, RepoDbUnitOfWork>();
}
```

Also, register the repositories as singleton.

```csharp
services.AddSingleton<IOrderRepository, OrderRepository>();
services.AddSingleton<IOrderItemRepository, OrderItemRepository>();
/* Do the same for the other repositories */
```

### Business Logic

In your business logic, make sure that the `IUnitOfWork` is being dependency injected. Let us assumed your business logic class is named `SalesManager` that implements the `ISalesManager` interface.

```csharp
public interface ISalesManager
{
    void SaveOrder(Order order,
        IEnumerable<OrderItem> orderItems);

    /* More business logic methods */
}

public class SalesManager : ISalesManager
{
    private IUnitOfWork unitOfWork;
    private IOrderRepository orderRepository;
    private IOrderItemRepository orderItemRepository;

    public SalesManager(IUnitOfWork unitOfWork,
        IOrderRepository orderRepository,
        IOrderItemRepository orderItemRepository,
        /* Other repositories here */)
    {
        // Attach the UOW
        orderRepository.Attach(unitOfWork);
        orderItemRepository.Attach(unitOfWork);
        /* Do the same for the other repositories */

        // Set the variables
        this.unitOfWork = unitOfWork;
        this.orderRepository = orderRepository;
        this.orderItemRepository = orderItemRepository;
        /* Do the same for the other repositories */
    }
}
```

In any of your business logic methods, ensure that you properly call the `Begin()`, `Commit()` and `Rollback()` methods of the `unitOfWork` variable.

Below is a sample method that saves the order and its order item.

```csharp
public void SaveOrders(Order order,
    IEnumerable<OrderItem> orderItems)
{
    // Start the UOW
    unitOfWork.Begin();

    try
    {
        // Call the repository methods
        var orderId = orderRepository.Save(order);
        orderItems = orderItems
            .AsList()
            .ForEach(e => e.OrderId = orderId);
        orderItemRepository.SaveAll(orderItems);

        // Commit
        unitOfWork.Commit();
    }
    catch
    {
        // Rollback
        unitOfWork.Rollback();
        throw;
    }
}
```

Lastly, do not forget to as well inject the business logic as transient.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<ISalesManager, SalesManager>();
}
```

After the registration of the business logic, you can always pass it on the constructor of your controllers.

> When working with UOW, you are obligated to always implement your own repository methods and call the repository methods underneath and explicitly pass the `transaction` object from the UOW object. You should avoid calling the normal operations of the repository directly as they are not a part of the transaction context. We in RepoDB are considering implementing the UOW in the near future versions, specifically the `Attach()` method mentioned on this page.