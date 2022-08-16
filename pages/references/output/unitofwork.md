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

### Interfaces

```csharp
public interface IUnitOfWork<TDbConnection>
{
    TDbConnection Connection { get; }
    DbTransaction Transaction { get ; }
    void Begin();
    void Rollback();
    void Commit();
}

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

public interface IOrderRepository : IRepository<Order, SqlConnection>
{
    ...
}

public interface IOrderItemRepository : IRepository<OrderItem, SqlConnection>
{
    ...
}

public interface ISalesManager
{
    void SaveOrder(Order order,
        IEnumerable<OrderItem> orderItems);

    /* More business logic methods */
}
```

### Classes

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

### Repositories

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

### Business Logics

```csharp
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
}
```

### Dependency Injection

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Unit Of Work
    services.AddTransient<IUnitOfWork, CustomUnitOfWork>();

    // Repositories
    services.AddSingleton<IOrderRepository, OrderRepository>();
    services.AddSingleton<IOrderItemRepository, OrderItemRepository>();
    /* Do the same for the other repositories */

    // Business Logics
    services.AddTransient<ISalesManager, SalesManager>();
}
```
