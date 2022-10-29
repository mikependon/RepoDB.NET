---
layout: page
title: "Generic Repository"
permalink: /reference/output/genericrepository
tags: [repodb, genericrepository]
parent: OUTPUT
grand_parent: REFERENCES
---

# Generic Repository

---

This page has the consolidated code of the [GenericRepository](/reference/genericrepository) reference implementation.

## Interface

```csharp
public interface IRepositoryBase<TDbConnection>
    where TDbConnection : DbConnection
{
    /*** Helper ***/

    TDbConnection GetConnection();

    /*** Non-Async ***/

    IEnumerable<TEntity> GetAll<TEntity>(string cacheKey = null);

    TEntity Get<TEntity>(object id);

    int Delete<TEntity>(object id);

    object Merge<TEntity>(TEntity entity);

    object Save<TEntity>(TEntity entity);

    int Update<TEntity>(TEntity entity);

    /*** Async ***/

    Task<IEnumerable<TEntity>> GetAllAsync<TEntity>(string cacheKey = null);

    Task<TEntity> GetAsync<TEntity>(object id);

    Task<int> DeleteAsync<TEntity>(object id);

    Task<objec> MergeAsync<TEntity>(TEntity entity);
    
    Task<object> SaveAsync<TEntity>(TEntity entity);

    Task<int> UpdateAsync<TEntity>(TEntity entity);
}
```

## Repository (Base)

```csharp
public class RepositoryBase<TDbConnection> : IRepositoryBase<TDbConnection>
    where TDbConnection : DbConnection
{
    private IOptions<AppSettings> _settings;

    public RepositoryBase(IOptions<AppSetting> settings,
        ICache cache,
        ITrace trace)
    {
        _settings = settings;
        Cache = cache;
        Trace = trace;
    }

    /*** Properties ***/

    public ITrace Trace { get; }

    public ICache Cache { get; }

    /*** Methods ***/
    
    public TDbConnection CreateConnection()
    {
        var connection = Activator.CreateInstance<TDbConnection>();
        connection.ConnectionString = _settings.Value.ConnectionString;
        return connection;
    }

    /*** Non-Async ***/

    // Get (Many)
        
    public IEnumerable<TEntity> GetAll<TEntity>(string cacheKey = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.QueryAll<TEntity>(cacheKey: cacheKey,
                commandTimeout: _settings.CommandTimeout,
                cache: Cache,
                cacheItemExpiration: _settings.CacheItemExpiration,
                trace: Trace);
        }
    }

    // Get

    public TEntity Get<TEntity>(int id)
    {
        using (var connection = CreateConnection())
        {
            return connection.Query<TEntity>(id,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Delete

    public int Delete<TEntity>(int id)
    {
        using (var connection = CreateConnection())
        {
            return connection.Delete<TEntity>(id,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Merge

    public int Merge<TEntity>(TEntity entity,
        IDbTransaction transaction = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.Merge<TEntity, int>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Save

    public int Save<TEntity>(TEntity entity,
        IDbTransaction transaction = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.Save<TEntity, int>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Update

    public int Update<TEntity>(TEntity entity,
        IDbTransaction transaction = null)
    {
        using (var connection = CreateConnection())
        {
            return Update<TEntity>(entity,
                transaction: transaction,
                trace: Trace);
        }
    }

    /*** Async ***/

    // Get (Many)

    public async Task<IEnumerable<TEntity>> GetAllAsync<TEntity>(string cacheKey = null)
    {
        using (var connection = CreateConnection())
        {
            return await connection.QueryAllAsync<TEntity>(cacheKey: cacheKey,
                commandTimeout: _settings.CommandTimeout,
                cache: Cache,
                cacheItemExpiration: _settings.CacheItemExpiration,
                trace: Trace);
        }
    }

    // Get

    public async Task<TEntity> GetAsync<TEntity>(int id)
    {
        using (var connection = CreateConnection())
        {
            return await connection.QueryAsync<TEntity>(id,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Delete

    public async Task<int> DeleteAsync<TEntity>(int id)
    {
        using (var connection = CreateConnection())
        {
            return await connection.DeleteAsync<TEntity>(id,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Merge

    public async Task<objec> MergeAsync<TEntity>(TEntity entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.MergeAsync<TEntity>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Save

    public async Task<object> SaveAsync<TEntity>(TEntity entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.SaveAsync<TEntity>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Update

    public async Task<int> UpdateAsync<TEntity>(TEntity entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.UpdateAsync<TEntity>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }
}
```

## Derived Repository Interface

```csharp
public interface INorthwindRepository
{
    /*** Non-Async ***/

    // Get

    Customer GetCustomer(int id);

    Order GetOrder(int id);

    Product GetProduct(int id);

    // Delete

    ...

    // Merge

    ...

    // Save

    int SaveCustomer(Customer customer);

    int SaveOrder(Order order);

    int SaveProduct(Product product);

    void SaveCustomerOrder(int customerId,
        int productId);
        
    // Update

    ...

    /*** Async **/

    ...
}
```

## Derived Repository

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings)
    { }

    /*** Non-Async ***/

    // Get
    
    public Customer GetCustomer(int id)
    {
        return base.Get<Customer>(customer);
    }

    public Order GetOrder(int id)
    {
        return base.Get<Order>(order);
    }

    public Product GetProduct(int id)
    {
        return base.Get<Product>(product);
    }

    // Delete

    ...

    // Merge

    ...

    // Save

    public int SaveCustomer(Customer customer)
    {
        return base.Save<Customer, int>(customer);
    }

    public int SaveOrder(Order order)
    {
        return base.Save<Order, int>(order);
    }

    public int SaveProduct(Product product)
    {
        return base.Save<Product, int>(product);
    }

    // Update

    ...

    // Unit of Work
    
    public void SaveCustomerOrder(int customerId,
        int productId)
    {
        using (var connection = CreateConnection())
        {
            using (var transaction = connection.BeginTransaction())
            {
                // Get the product
                var product = connection.Query<Product>(productId, transaction: transaction).FirstOrDefault();

                // Save the order
                var order = new Order
                {
                    CustomerId = customerId,
                    ProductId = productId,
                    CurrentPrice = product.Price,
                    OrderDateUtc = DateTime.UtcNow
                };
                order.CustomerId = customerId;
                var orderId = connection.Save<Order>(order, transaction: transaction);

                // Commit the transaction
                transaction.Commit();
            }
        }
    }

    /*** Async ***/
    
    ...
}
```

## Settings

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
    public int CacheItemExpiration { get; set; }
}
```

## Dependency Injection

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindRepository, NorthwindRepository>();
}
```

## Cache

```csharp
// Custom Class
public static class MyCustomCache : MemoryCache
{
    ...
}

// Factory
public static class CacheFactory
{
    private static object _syncLock = new object();
    private static ICache _cache = null;
    
    public static ICache CreateCacher()
    {
        if (_cache == null)
        {
            lock (_syncLock)
            {
                if (_cache == null)
                {
                    _cache = new MyCustomCache();
                }
            }
        }
        return _cache;
    }
}

// Dependency Injection
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ICache, MyCustomCache>();
}
```

## Trace

```csharp
// Custom Class
public static class MyCustomTrace : ITrace
{
    /* Implement all the methods here */
}

// Factory
public static class TraceFactory
{
    private static object _syncLock = new object();
    private static ITrace _trace = null;
    
    public static ITrace CreateTracer()
    {
        if (_trace == null)
        {
            lock (_syncLock)
            {
                if (_trace == null)
                {
                    _trace = new MyCustomTrace();
                }
            }
        }
        return _trace;
    }
}

// Dependency Injection
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ITrace, MyCustomTrace>();
}
```
