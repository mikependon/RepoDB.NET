---
layout: default
sidebar: references
title: BaseRepository
nav_order: 1
permalink: /reference/baserepository
tags: [repodb, baserepository]
parent: REFERENCES
---

# BaseRepository

---

This page contains the reference implementation for a repository that inherits the [BaseRepository](/class/baserepository) class. The consolidated output can be found [here](/reference/output/baserepository).

{: .note }
> The [BaseRepository](/class/baserepository) class is intended for entity-based repositories. Use it when working with a single table, such as `[dbo].[Customer]`.

#### Recommended Objects (Optional)

- [ICache](/interface/icache)
- [ITrace](/interface/itrace)

#### Recommended Properties (Optional)

- [ConnectionPersistency](/enumeration/connectionpersistency)
- [CommandTimeout](https://docs.microsoft.com/en-us/dotnet/api/microsoft.data.sqlclient.sqlcommand.commandtimeout?view=sqlclient-dotnet-core-1.1)

## Cache

Create a custom cache class.

```csharp
public static class MyCustomCache : MemoryCache
{
    ...
}
```

Create a factory class.

```csharp
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
```

Or, use dependency injection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ICache, MyCustomCache>();
}
```

## Trace

Create a custom trace class.

```csharp
public static class MyCustomTrace : ITrace
{
    /* Implement all the methods here */
}
```

Create a factory class.

```csharp
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
```

Or, use dependency injection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ITrace, MyCustomTrace>();
}
```

## Settings

Inject the settings object via the repository constructor. See the Microsoft [documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1) for details.

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
    public int CacheItemExpiration { get; set; }
}
```

## Repository

Inherit the [BaseRepository](/class/baserepository) class and provide the entity model and connection object as generic type arguments.

Using factory classes:

```csharp
public class CustomerRepository : BaseRepository<Customer, SqlConnection>
{
    public CustomerRepository(IOptions<AppSetting> settings)
        : base(settings.Value.ConnectionString,
            commandTimeout: settings.CommandTimeout,
            connectionPersistency: ConnectionPersistency.PerCall,
            cache: CacheFactory.CreateCacher(),
            cacheItemExpiration: settings.CacheItemExpiration,
            trace: TraceFactory.CreateTracer(),
            statementBuilder: null)
    { }

    ...
}
```

Using dependency injection:

```csharp
public class CustomerRepository : BaseRepository<Customer, SqlConnection>
{
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

    ...
}
```

## Methods

Recommended implementation for a method that returns all records:

```csharp
public IEnumerable<Customer> GetAll(string cacheKey = null,
    IDbTransaction transaction = null)
{
    return QueryAll(cacheKey: cacheKey,
        transaction: transaction);
}
```

Recommended implementation for a method that returns a single record:

```csharp
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
```

Recommended implementation for a method that deletes a record:

```csharp
public int Delete(int id,
    IDbTransaction transaction = null)
{
    return base.Delete(id,
        transaction: transaction);
}
```

Recommended implementation for methods that write a record:

```csharp
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
```

## Async Methods

Each synchronous method must have a corresponding asynchronous counterpart with the `Async` suffix, delegating to the library's async operations.

```csharp
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
```

## Dependency Injection

Create an interface covering all synchronous and asynchronous methods. Name the interface to reflect the repository's purpose.

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

Then implement it on the repository.

```csharp
public class CustomerRepository : BaseRepository<Customer, SqlConnection>, ICustomerRepository
{
    ...
}
```

## Service Configuration and Registration

Register as singleton when:

- Using the [ConnectionPersistency.Instance](/enumeration/connectionpersistency#instance) enumeration.
- [ICache](/interface/icache) is enabled.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ICustomerRepository, CustomerRepository>();
}
```

Otherwise, register as transient.


```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<ICustomerRepository, CustomerRepository>();
}
```

## Key Take-aways

- Pass the transaction argument in every method to support the Unit of Work (UOW) pattern.
- Pass the cache key argument to enable result caching.
- Define an interface to support dependency injection.
- Register as singleton when caching or connection persistency is required.
- Keep each repository focused on a single, well-defined purpose.