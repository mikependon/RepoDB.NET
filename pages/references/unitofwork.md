---
layout: default
sidebar: references
title: "Unit of Work"
nav_order: 11
permalink: /reference/unitofwork
tags: [repodb, unitofwork]
parent: REFERENCES
---

# Unit of Work

---

This page contains the recommended way of implementing a Unit of Work (UOW) object when using this library. The consolidated output of this page can be found [here](/reference/output/unitofwork).

## Interface

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

Then, save the following properties that will hold the state of transaction.

| Name | Description |
|:-----|:------------|
| Connection | Will hold the instance of the connection object. |
| Transaction | Will hold the instance of the active transaction. |

And, create the 3 basic transactional methods.

| Name | Description |
|:-----|:------------|
| Begin | Is used to begin a new transaction. |
| Commit | Is used to commit an existing transaction. |
| Rollback | Is used to rollback an existing transaction. |

## Class

Create a class that implements the newly created interface. Here, let us assume we have the `AppSettings` class as the configuration class.

```csharp
public class CustomUnitOfWork : IUnitOfWork<SqlConnection>
{
    private AppSettings _appSettings;
    private SqlConnection _connection;
    private DbTransaction _transaction;

    public CustomUnitOfWork(IOptions<AppSettings> options)
    {
        _appSettings = options.Value;
    }

    public SqlConnection Connection => _connection;

    public DbTransaction Transaction => _transaction;

    public void Begin()
    {
        if (_transaction != null)
        {
            throw new InvalidOperationException("Cannot start a new transaction while the existing one is still open.");
        }
        _connection = _connection ??= (new SqlConnection(_appSettings.ConnectionString)).EnsureOpen();
        _transaction = _connection.BeginTransaction();
    }

    public void Commit()
    {
        if (_transaction == null)
        {
            throw new InvalidOperationException("There is no active transaction to commit.");
        }
        using (_transaction)
        {
            _transaction.Commit();
        }
        _transaction = null;
    }

    public void Rollback()
    {
        if (_transaction == null)
        {
            throw new InvalidOperationException("There is no active transaction to rollback.");
        }
        using (_transaction)
        {
            _transaction.Rollback();
        }
        _transaction = null;
    }
}
```

> Please note that the corresponding asynchronous methods were not implemented on this sample. You have to introduce it yourself if you wish to have it covered.

## Repositories

First, implement the base interface that contains the necessary methods.

```csharp
public interface IRepository<TEntity, TDbConnection>
    where TEntity : class
    where TDbConnection : IDbConnection
{
    void Attach(IUnitOfWork<TDbConnection> unitOfWork);
    TResult Save<TResult>(TEntity entity);
    int SaveAll(IEnumerable<TEntity> entities);
    int Delete(object id);
    TResult Merge<TResult>(TEntity entity);
    TEntity Query(object id);
    int Update(TEntity entity);
}
```

Then, implement the entity level interface repository.

```csharp
public interface IOrderRepository : IRepository<Order, SqlConnection>
{
    ...
}

public interface IOrderItemRepository : IRepository<OrderItem, SqlConnection>
{
    ...
}
```

Then, implement a base repository that has the unit-of-work capability. Let us call this repository `EntityRepository`.

```csharp
public class EntityRepository<TEntity> : BaseRepository<TEntity, SqlConnection>,
    IRepository<TEntity, SqlConnection>
    where TEntity : class
{
    private IUnitOfWork<SqlConnection> _unitOfWork;

    public EntityRepository(IOptions<AppSettings> options)
        : base(options.Value.ConnectionString) { }

    public void Attach(IUnitOfWork<SqlConnection> unitOfWork) =>
        _unitOfWork = unitOfWork;

    public TResult Save<TResult>(TEntity entity) =>
        Insert<TResult>(entity,
            transaction: _unitOfWork.Transaction);

    public int SaveAll(IEnumerable<TEntity> entities) =>
        InsertAll(entities,
            transaction: _unitOfWork.Transaction);

    public int Delete(object id) =>
        Delete(id,
            transaction: _unitOfWork.Transaction);

    public TResult Merge<TResult>(TEntity entity) =>
        Merge<TResult>(entity,
            transaction: _unitOfWork?.Transaction);

    public TEntity Query(object id) =>
        Query(id,
            transaction: _unitOfWork?.Transaction)?.FirstOrDefault();

    public int Update(TEntity entity) =>
        Update(entity,
            transaction: _unitOfWork?.Transaction);
}
```

> Take note of the method `Attach()`, it accepts an instance of `IUnitOfWork<SqlConnection>` object. This method will be used in the business logic to passed the currently injected (in-used) UOW class. Also, the variable `_unitOfWork` will hold an instance of `IUnitOfWork<SqlConnection>` interface that has been created when the `Attach()` method is called.

Then, create a repository class that inherits either the [DbRepository](/class/dbrepository) or [BaseRepository](/class/baserepository). On this class, implement the newly created `I<Entity>Repository` interface, the one that implements the `IRepository<TEntity, TDbConnection>` interface.

```csharp
public class OrderRepository : EntityRepository<Order>, IOrderRepository
{
    public OrderRepository(IOptions<Settings> options)
        : base(options)
    { }
}

public class OrderItemRepository : EntityRepository<OrderItem>, IOrderItemRepository
{
    public OrderRepository(IOptions<Settings> options)
        : base(options)
    { }
}
```

> Please note that the corresponding asynchronous methods were not implemented on this sample. You have to introduce it yourself if you wish to have it covered.

## Dependency Injection

Register the UOW interface and class via service registration. Ensure it is on transient level.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.SaveControllers();

    // Registration
    services.AddTransient<IUnitOfWork<SqlConnection>, CustomUnitOfWork>();
}
```

Also, register the repositories as singleton.

```csharp
services.AddSingleton<IOrderRepository, OrderRepository>();
services.AddSingleton<IOrderItemRepository, OrderItemRepository>();
/* Do the same for the other repositories */
```

## Business Logic

In your business logic, make sure that the `IUnitOfWork<SqlConnection>` is being dependency injected. Let us assumed your business logic class is named `SalesManager` that implements the `ISalesManager` interface.

```csharp
public interface ISalesManager
{
    void SaveOrder(Order order,
        IEnumerable<OrderItem> orderItems);

    /* More business logic methods */
}

public class SalesManager : ISalesManager
{
    private IUnitOfWork<SqlConnection> _unitOfWork;
    private IOrderRepository _orderRepository;
    private IOrderItemRepository _orderItemRepository;

    public SalesManager(IUnitOfWork unitOfWork,
        IOrderRepository orderRepository,
        IOrderItemRepository orderItemRepository,
        /* Other repositories here */)
    {
        _unitOfWork = unitOfWork;
        _orderRepository = orderRepository;
        _orderItemRepository = orderItemRepository;

        // Attach the UOW
        _orderRepository.Attach(_unitOfWork);
        _orderItemRepository.Attach(_unitOfWork);
    }
}
```

In any of your business logic methods, ensure that you properly call the `Begin()`, `Commit()` and `Rollback()` methods of the `_unitOfWork` variable.

Below is a sample method that saves the order and its order item.

```csharp
public void SaveOrders(Order order,
    IEnumerable<OrderItem> orderItems)
{
    // Start the UOW
    _unitOfWork.Begin();

    try
    {
        // Call the repository methods
        var orderId = orderRepository.Save(order);
        orderItems
            .AsList()
            .ForEach(e => e.OrderId = orderId);
        _orderItemRepository.SaveAll(orderItems);

        // Commit
        _unitOfWork.Commit();
    }
    catch
    {
        // Rollback
        _unitOfWork.Rollback();
    }
}
```

Lastly, do not forget to as well inject the business logic as transient.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.SaveControllers();

    // Registration
    services.SaveTransient<ISalesManager, SalesManager>();
}
```

After the registration of the business logic, you can always pass it on the constructor of your controllers.

> When working with UOW, you are obligated to always implement your own repository methods and call the repository methods underneath and explicitly pass the `transaction` object from the UOW object. You should avoid calling the normal operations of the repository directly as they are not a part of the transaction context. We in RepoDB are considering implementing the UOW in the near future versions, specifically the `Attach()` method mentioned on this page.
