---
layout: page
title: BaseRepository
permalink: /reference/output/baserepository
tags: [repodb, baserepository]
parent: OUTPUT
grand_parent: REFERENCES
---

# BaseRepository

---

This page has the consolidated code of the [BaseRepository](/reference/baserepository) reference implementation.

```csharp
public class CustomerRepository : BaseRepository<Customer, SqlConnection>, ICustomerRepository
{
    // Factory Classes
    public CustomerRepository(IOptions<AppSetting> settings)
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
    public CustomerRepository(IOptions<AppSetting> settings,
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

    public int Merge(Customer customer,
        IDbTransaction transaction = null)
    {
        return base.Merge<int>(customer,
            transaction: transaction);
    }

    public int Save(Customer customer,
        IDbTransaction transaction = null)
    {
        return Insert<int>(customer,
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

    public async Task<int> MergeAsync(Customer customer,
        IDbTransaction transaction = null)
    {
        return await base.MergeAsync<int>(customer,
            transaction: transaction);
    }

    public async Task<int> SaveAsync(Customer customer,
        IDbTransaction transaction = null)
    {
        return await InsertAsync<int>(customer,
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

## Interface

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

    int Merge(Customer customer,
        IDbTransaction transaction = null);

    int Save(Customer customer,
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

    Task<int> Merge(Customer customer,
        IDbTransaction transaction = null);

    Task<int> SaveAsync(Customer customer,
        IDbTransaction transaction = null);

    Task<int> UpdateAsync(Customer customer,
        IDbTransaction transaction = null);
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

## Dependency Injections

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
