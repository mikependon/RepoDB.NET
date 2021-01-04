---
layout: page
title: "Unit of Work"
permalink: /reference/output/unitofwork
tags: [repodb, class, unitofwork, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: OUTPUT
grand_parent: REFERENCES
---

# Unit of Work

---

This page has the consolidated code of the [Unit of Work](/reference/unitofwork) reference implementation.

### Interface

```csharp
public interface IUnitOfWork<TDbConnection>
{
    TDbConnection Connection { get; }
    DbTransaction Transaction { get ; }
    void Begin();
    void Rollback();
    void Commit();
}

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

public interface IOrderRepository : IRepository<Order>
{
    /* No order specific methods for now */
}

public interface ISalesManager
{
    void SaveOrder(Order order,
        IEnumerable<OrderItem> orderItems);

    /* More business logic methods */
}
```

### Class

```csharp
public class RepoDbUnitOfWork : IUnitOfWork<SqlConnection>
{
    /* Privates */

    private AppSettings settings;
    private SqlConnection connection;
    private DbTransaction transaction;

    /* Constructors */

    public RepoDbUnitOfWork(IOptions<AppSettings> settings)
    {
        this.settings = settings.Value;
    }

    /* Properties */

    public SqlConnection Connection { get { return connection; } }
    
    public DbTransaction Transaction { get { return transaction; } }

    /* Custom Methods */

    private SqlConnection EnsureConnection() =>
        connection ?? connection = new SqlConnection(settings.ConnectionString);

    /* Interface Methods */

    public void Start()
    {
        if (transaction != null)
        {
            throw new InvalidOperationException("Cannot start a new transaction while the existing other one is still open.");
        }
        var connection = EnsureConnection();
        transaction = connection.BeginTransaction();
    }

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
}
```

### Repository

```csharp

public class OrderRepository : BaseRepository<Order, SqlConnection>, IOrderRepository
{
    /* Privates */

    private UnitOfWork unitOfWork;

    /* Constructors */

    public OrderRepository(IOptions<Settings> settings)
        : base(settings.Value.ConnectionString)
    { }

    /* Interface Methods */

    public void Attach(IUnitOfWork unitOfWork)
    {
        this.unitOfWork = unitOfWork;
    }

    public TResult Add<TResult>(Order entity)
    {
        return Insert<Order, TResult>(entity,
            transaction: unitOfWork?.Transaction);
    }

    public int AddAll(IEnumerable<Order> entities)
    {
        return InsertAll<Order>(entities,
            transaction: unitOfWork?.Transaction);
    }

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

    public TResult Merge<TResult>(Order entity)
    {
        return Merge<Order, TResult>(entity,
            transaction: unitOfWork?.Transaction);
    }

    public TResult Query(object id)
    {
        return Query<Order>(id,
            transaction: unitOfWork?.Transaction);
    }

    public int Update(Order entity)
    {
        return Update<Order>(entity,
            transaction: unitOfWork?.Transaction);
    }
}
```

### Business Logic

```csharp
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
}
```

### Dependency Injection

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Unit Of Work
    services.AddTransient<IUnitOfWork, RepoDbUnitOfWork>();

    // Repositories
    services.AddSingleton<IOrderRepository, OrderRepository>();
    services.AddSingleton<IOrderRepository, OrderRepository>();
    services.AddSingleton<IOrderItemRepository, OrderItemRepository>();
    /* Do the same for the other repositories */

    // Business Logics
    services.AddTransient<ISalesManager, SalesManager>();
}
```
