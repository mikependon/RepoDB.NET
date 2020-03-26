---
layout: page
title: "ConnectionRepository Reference Output (RepoDb)"
permalink: /reference/output/connectionrepository
tags: [repodb, class, connectionrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

#### NorthwindRepository (Connection-Based Repository)

```csharp
public class NorthwindRepository : ConnectionRepository<Customer, SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings.ConnectionString,
            commandTimeout: 0,
            connectionPersistency: ConnectionPersistency.PerCall,
            cache: CacheFactory.CreateCacher(),
            cacheItemExpiration: 180 /* 3 hours */,
            trace: TraceFactory.CreateTracer(),
            statementBuilder: null /* Not necessary */ )
    { }

    /*** Sync ***/

    // Get (Many)

    public IEnumerable<Customer> GetCustomers(string cacheKey = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.QueryAll<Customer>(cacheKey: cacheKey,
                commandTimeout: m_settings.CommandTimeout,
                cache: Cache,
                cacheItemExpiration: m_settings.CacheItemExpiration,
                trace: Trace);
        }
    }

    // Get

    public Customer GetCustomer(object id)
    {
        using (var connection = CreateConnection())
        {
            return connection.Query<Customer>(id,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Delete

    public int DeleteCustomer(object id)
    {
        using (var connection = CreateConnection())
        {
            return connection.Delete<Customer>(id,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Merge

    public object MergeCustomer(Customer entity,
        IDbTransaction transaction = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.Merge<Customer>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Save

    public object SaveCustomer(Customer entity,
        IDbTransaction transaction = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.Insert<Customer>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Update

    public int UpdateCustomer(Customer entity,
        IDbTransaction transaction = null)
    {
        using (var connection = CreateConnection())
        {
            return Update<Customer>(entity,
                transaction: transaction,
                trace: Trace);
        }
    }
    
    /*** Async ***/

    // Get (Many)

    public async Task<IEnumerable<Customer>> GetCustomersAsync(string cacheKey = null)
    {
        using (var connection = CreateConnection())
        {
            return await connection.QueryAllAsync<Customer>(cacheKey: cacheKey,
                commandTimeout: m_settings.CommandTimeout,
                cache: Cache,
                cacheItemExpiration: m_settings.CacheItemExpiration,
                trace: Trace);
        }
    }

    // Get

    public async Task<Customer> GetAsync(object id)
    {
        using (var connection = CreateConnection())
        {
            return await connection.QueryAsync<Customer>(id,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Delete

    public async Task<int> DeleteAsync(object id)
    {
        using (var connection = CreateConnection())
        {
            return await connection.DeleteAsync<Customer>(id,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Merge

    public async Task<objec> MergeAsync(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.MergeAsync<Customer>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Save

    public async Task<object> SaveAsync(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.SaveAsync<Customer>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Update

    public async Task<int> UpdateAsync(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.UpdateAsync<Customer>(entity,
                commandTimeout: m_settings.CommandTimeout,
                trace: Trace);
        }
    }
}
```

#### Settings

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
}
```

#### Dependency Injection

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
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
