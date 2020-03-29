---
layout: page
title: "BaseRepository Reference Output (RepoDb)"
permalink: /reference/output/baserepository
tags: [repodb, class, baserepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

#### CustomerRepository (BaseRepository)

```csharp
public class CustomerRepository : BaseRepository<Customer, SqlConnection>, ICustomerRepository
{
    public CustomerRepository(IOptions<AppSetting> settings)
        : base(settings.ConnectionString,
            commandTimeout: settings.CommandTimeout,
            connectionPersistency: ConnectionPersistency.PerCall,
            cache: CacheFactory.CreateCacher(),
            cacheItemExpiration: settings.CacheItemExpiration,
            trace: TraceFactory.CreateTracer(),
            statementBuilder: null /* Not necessary */ )
    { }

    // Sync

    public IEnumerable<Customer> GetAll(string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return QueryAll(cacheKey: cacheKey,
            transaction: transaction);
    }

    public Customer Get(int id,
        string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return Query(id,
            cacheKey: cacheKey,
            transaction: transaction).FirstOrDefault();
    }

    public Customer GetByName(string name,
        string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return Query(p => p.Name == name,
            cacheKey: cacheKey,
            transaction: transaction).FirstOrDefault();
    }
    
    public int Delete(int id,
        IDbTransaction transaction = null)
    {
        return base.Delete(id,
            transaction: transaction);
    }

    public object Merge(Customer customer,
        IDbTransaction transaction = null)
    {
        return base.Merge(customer,
            transaction: transaction);
    }

    public object Save(Customer customer,
        IDbTransaction transaction = null)
    {
        return Insert(customer,
            transaction: transaction);
    }

    public int Update(Customer customer,
        IDbTransaction transaction = null)
    {
        return base.Update(customer,
            transaction: transaction);
    }

    // Async

    public async Task<IEnumerable<Customer>> GetAllAsync(string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return await QueryAllAsync(cacheKey: cacheKey,
            transaction: transaction);
    }

    public async Task<Customer> GetAsync(int id,
        string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return (await QueryAsync(id,
            cacheKey: cacheKey,
            transaction: transaction)).FirstOrDefault();
    }

    public async Task<Customer> GetByNameAsync(string name,
        string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return (await QueryAsync(p => p.Name == name,
            cacheKey: cacheKey,
            transaction: transaction)).FirstOrDefault();
    }

    public async Task<int> DeleteAsync(int id,
        IDbTransaction transaction = null)
    {
        return await base.DeleteAsync(id,
            transaction: transaction);
    }

    public async Task<object> MergeAsync(Customer customer,
        IDbTransaction transaction = null)
    {
        return await base.MergeAsync(customer,
            transaction: transaction);
    }

    public async Task<object> SaveAsync(Customer customer,
        IDbTransaction transaction = null)
    {
        return await InsertAsync(customer,
            transaction: transaction);
    }

    public async Task<int> UpdateAsync(Customer customer,
        IDbTransaction transaction = null)
    {
        return await base.UpdateAsync(customer,
            transaction: transaction);
    }
}
```

#### Interface

```csharp
public interface ICustomerRepository
{
    // Non-Async

    IEnumerable<Customer> GetAll(string cacheKey = null,
        IDbTransaction transaction = null);

    Customer Get(int id,
        string cacheKey = null,
        IDbTransaction transaction = null);

    Customer GetByName(string name,
        string cacheKey = null,
        IDbTransaction transaction = null);

    int Delete(int id,
        IDbTransaction transaction = null);

    object Merge(Customer customer,
        IDbTransaction transaction = null);

    object Save(Customer customer,
        IDbTransaction transaction = null);

    int Update(Customer customer,
        IDbTransaction transaction = null);

    // Async

    Task<IEnumerable<Customer>> GetAllAsync(string cacheKey = null,
        IDbTransaction transaction = null);

    Task<Customer> GetAsync(int id,
        string cacheKey = null,
        IDbTransaction transaction = null);

    Task<Customer> GetByNameAsync(string name,
        string cacheKey = null,
        IDbTransaction transaction = null);

    Task<int> DeleteAsync(int id,
        IDbTransaction transaction = null);

    Task<object> Merge(Customer customer,
        IDbTransaction transaction = null);

    Task<object> SaveAsync(Customer customer,
        IDbTransaction transaction = null);

    Task<int> UpdateAsync(Customer customer,
        IDbTransaction transaction = null);
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

#### Dependency Injections

For singleton.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ICustomerRepository, CustomerRepository>();
}
```

Or for transient.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<ICustomerRepository, CustomerRepository>();
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
