---
layout: page
title: "Connection-Based Repository"
permalink: /reference/output/connectionrepository
tags: [repodb, class, connectionrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Output
grand_parent: References
---

# Connection-Based Repository

---

This page has the consolidated code of the [Connection-Based Repository](/reference/connectionrepository) reference implementation.

```csharp
public class NorthwindRepository : ConnectionRepository<Customer, SqlConnection>, INorthwindRepository
{
    // Factory Classes
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings.Value.ConnectionString,
            commandTimeout: settings.CommandTimeout,
            connectionPersistency: ConnectionPersistency.PerCall,
            cache: CacheFactory.CreateCacher(),
            cacheItemExpiration: settings.CacheItemExpiration,
            trace: TraceFactory.CreateTracer(),
            statementBuilder: null)
    { }

    // Dependency Injected Classes
    /*
    public NorthwindRepository(IOptions<AppSetting> settings,
        ICache cache,
        ITrace trace)
        : base(settings.Value.ConnectionString,
            commandTimeout: settings.CommandTimeout,
            connectionPersistency: ConnectionPersistency.PerCall,
            cache: cache,
            cacheItemExpiration: settings.CacheItemExpiration,
            trace: trace,
            statementBuilder: null)
    { }
    */

    /*** Sync ***/

    // Get (Many)

    public IEnumerable<Customer> GetCustomers(string cacheKey = null)
    {
        using (var connection = CreateConnection())
        {
            return connection.QueryAll<Customer>(cacheKey: cacheKey,
                commandTimeout: _settings.CommandTimeout,
                cache: Cache,
                cacheItemExpiration: _settings.CacheItemExpiration,
                trace: Trace);
        }
    }

    // Get

    public Customer GetCustomer(object id)
    {
        using (var connection = CreateConnection())
        {
            return connection.Query<Customer>(id,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Delete

    public int DeleteCustomer(object id)
    {
        using (var connection = CreateConnection())
        {
            return connection.Delete<Customer>(id,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Merge

    public object MergeCustomer(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return connection.Merge<Customer>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Save

    public object SaveCustomer(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return connection.Insert<Customer>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Update

    public int UpdateCustomer(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return connection.Update<Customer>(entity,
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
                commandTimeout: _settings.CommandTimeout,
                cache: Cache,
                cacheItemExpiration: _settings.CacheItemExpiration,
                trace: Trace);
        }
    }

    // Get

    public async Task<Customer> GetCustomerAsync(object id)
    {
        using (var connection = CreateConnection())
        {
            return await connection.QueryAsync<Customer>(id,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Delete

    public async Task<int> DeleteCustomerAsync(object id)
    {
        using (var connection = CreateConnection())
        {
            return await connection.DeleteAsync<Customer>(id,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Merge

    public async Task<objec> MergeCustomerAsync(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.MergeAsync<Customer>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Save

    public async Task<object> SaveCustomerAsync(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.SaveAsync<Customer>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }

    // Update

    public async Task<int> UpdateCustomerAsync(Customer entity)
    {
        using (var connection = CreateConnection())
        {
            return await connection.UpdateAsync<Customer>(entity,
                commandTimeout: _settings.CommandTimeout,
                trace: Trace);
        }
    }
}
```

### Interface

```csharp
public interface INorthwindRepository<TDbConnection>
    where TDbConnection : DbConnection
{
    /*** Helper ***/

    TDbConnection GetConnection();

    /*** Non-Async ***/

    IEnumerable<Customer> GetCustomers(string cacheKey = null);

    Customer GetCustomer(object id);

    int DeleteCustomer(object id);

    object MergeCustomer(Customer entity);

    object SaveCustomer(Customer entity);

    int Update<Customer>(Customer entity);

    /*** Async ***/

    Task<IEnumerable<Customer>> GetCustomersAsync(string cacheKey = null);

    Task<Customer> GetCustomerAsync(object id);

    Task<int> DeleteCustomerAsync(object id);

    Task<objec> MergeCustomerAsync(Customer entity);
    
    Task<object> SaveCustomerAsync(Customer entity);

    Task<int> UpdateCustomerAsync(Customer entity);
}
```

### Settings

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
    public int CacheItemExpiration { get; set; }
}
```

### Dependency Injection

For singleton.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindRepository, NorthwindRepository>();
}
```

Or for transient.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

### Cache

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

### Trace

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
