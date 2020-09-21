---
layout: page
title: "GenericRepository Reference Output"
permalink: /reference/output/genericrepository
tags: [repodb, class, genericrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

#### NorthwindRepository (DerivedRepository)

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings)
    { }

    /*** Non-Async ***/

    // Get
    
    public Customer GetCustomer(object id)
    {
        return base.Get<Customer>(customer);
    }

    public Order GetOrder(object id)
    {
        return base.Get<Order>(order);
    }

    public Product GetProduct(object id)
    {
        return base.Get<Product>(product);
    }

    // Delete

    ...

    // Merge

    ...

    // Save

    public object SaveCustomer(Customer customer)
    {
        return base.Save<Customer>(customer);
    }

    public object SaveOrder(Order order)
    {
        return base.Save<Order>(order);
    }

    public object SaveProduct(Product product)
    {
        return base.Save<Product>(product);
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

#### Derived Repository Interface

```csharp
public interface INorthwindRepository
{
    /*** Non-Async ***/
    
    // Get

    Customer GetCustomer(object id);

    Order GetOrder(object id);

    Product GetProduct(object id);

    // Delete

    ...

    // Merge

    ...

    // Save

    object SaveCustomer(Customer customer);

    object SaveOrder(Order order);

    object SaveProduct(Product product);

    void SaveCustomerOrder(int customerId,
        int productId);
        
    // Update

    ...

    /*** Async **/

    ...
}
```

#### RepositoryBase (GenericRepository)

```csharp
public class RepositoryBase<TDbConnection> : IRepositoryBase<TDbConnection>
    where TDbConnection : DbConnection
{
    private IOptions<AppSettings> m_settings;

    public RepositoryBase(IOptions<AppSetting> settings)
    {
        m_settings = settings;
        Cache = CacheFactory.CreateCacher();
        Trace = TraceFactory.CreateTracer();
    }

    /*** Properties ***/

    public ITrace Trace { get; }

    public ICache Cache { get; }

    /*** Methods ***/
    
    public TDbConnection CreateConnection()
    {
        var connection = Activator.CreateInstance<TDbConnection>();
        connection.ConnectionString = m_settings.ConnectionString;
        return connection;
    }

    /*** Non-Async ***/

    // Get (Many)
        
    public IEnumerable<TEntity> GetAll<TEntity>(string cacheKey = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.QueryAll<TEntity>(cacheKey: cacheKey,
                commandTimeout: m_settings.CommandTimeout,
                cache: Cache,
                cacheItemExpiration: m_settings.CacheItemExpiration,
                trace: Trace);
        }
    }

    // Get

    public TEntity Get<TEntity>(object id)
    {
        using (var connection = CreateConnection())
        {
            return connection.Query<TEntity>(id,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Delete

    public int Delete<TEntity>(object id)
    {
        using (var connection = CreateConnection())
        {
            return connection.Delete<TEntity>(id,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Merge

    public object Merge<TEntity>(TEntity entity,
        IDbTransaction transaction = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.Merge<TEntity>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Save

    public object Save<TEntity>(TEntity entity,
        IDbTransaction transaction = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.Save<TEntity>(entity,
                commandTimeout: m_settings.CommandTimeout,
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
                commandTimeout: m_settings.CommandTimeout,
                cache: Cache,
                cacheItemExpiration: m_settings.CacheItemExpiration,
                trace: Trace);
        }
    }

    // Get

    public async Task<TEntity> GetAsync<TEntity>(object id)
    {
        using (var connection = CreateConnection())
        {
            return await connection.QueryAsync<TEntity>(id,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Delete

    public async Task<int> DeleteAsync<TEntity>(object id)
    {
        using (var connection = CreateConnection())
        {
            return await connection.DeleteAsync<TEntity>(id,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Merge

    public async Task<objec> MergeAsync<TEntity>(TEntity entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.MergeAsync<TEntity>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Save

    public async Task<object> SaveAsync<TEntity>(TEntity entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.SaveAsync<TEntity>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Update

    public async Task<int> UpdateAsync<TEntity>(TEntity entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.UpdateAsync<TEntity>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }
}
```

#### Interface (RepositoryBase)

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

#### Settings

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
    public int CacheItemExpiration { get; set; }
}
```

#### Dependency Injection

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindRepository, NorthwindRepository>();
}
```

#### CacheFactory

```csharp
public static class CacheFactory
{
    private static object m_syncLock = new object();
    private static ICache m_cache = null;
    
    public static ICache CreateCacher()
    {
        if (m_cache == null)
        {
            lock (m_syncLock)
            {
                if (m_cache == null)
                {
                    m_cache = new MyCustomCache();
                }
            }
        }
        return m_cache;
    }
}
```

#### TraceFactory

```csharp
public static class TraceFactory
{
    private static object m_syncLock = new object();
    private static ITrace m_trace = null;
    
    public static ITrace CreateTracer()
    {
        if (m_trace == null)
        {
            lock (m_syncLock)
            {
                if (m_trace == null)
                {
                    m_trace = new MyCustomTrace();
                }
            }
        }
        return m_trace;
    }
}
```
