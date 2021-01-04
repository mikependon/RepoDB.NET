---
layout: default
sidebar: references
title: BaseRepository
nav_order: 1
permalink: /reference/baserepository
tags: [repodb, class, baserepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: REFERENCES
---

# BaseRepository

---

This page contains the reference implementation when implementing a repository that inherits the [BaseRepository](/class/baserepository) class. The consolidated output of this page can be found [here](/reference/output/baserepository).

> The [BaseRepository](/class/baserepository) class is only being used if you wished to implement an entity-based repository. Imagine that you are only working with `[dbo].[Customer]` table.

#### Recommended Objects (Optional)

- [ICache](/interface/icache)
- [ITrace](/interface/itrace)

#### Recommended Properties (Optional)

- [ConnectionPersistency](/enumeration/connectionpersistency)
- [CommandTimeout](https://docs.microsoft.com/en-us/dotnet/api/microsoft.data.sqlclient.sqlcommand.commandtimeout?view=sqlclient-dotnet-core-1.1)

### Cache

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

Or, if you wish to dependency inject.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ICache, MyCustomCache>();
}
```

### Trace

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

Or, if you wish to dependency inject.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ITrace, MyCustomTrace>();
}
```

### Settings

The settings object must be injected within the constructor of the repository. Please refer to Microsoft [documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1).

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
    public int CacheItemExpiration { get; set; }
}
```

### Repository

Simply inherit the [BaseRepository](/class/baserepository) class and pass the generic types for the entity-model and connection object.

Below is the sample repository implementation.

For the factory classes.

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

For the dependency-injected classes.

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

### Methods

Below is the recommended way when exposing a method that returns all the records.

```csharp
public IEnumerable<Customer> GetAll(string cacheKey = null,
    IDbTransaction transaction = null)
{
    return QueryAll(cacheKey: cacheKey,
        transaction: transaction);
}
```

Below is the recommended way when exposing a method that returns a single record.

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

Below is the recommended way when exposing a method that deletes a record.

```csharp
public int Delete(int id,
    IDbTransaction transaction = null)
{
    return base.Delete(id,
        transaction: transaction);
}
```

Below is the recommended way when exposing a method that pushes a record.

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

### Async Methods

Ensure that all the synchronous methods you had created has the corresponding asynchronous methods suffixed by `Async` keyword. Within these methods, ensure that you are calling the corresponding asynchronous operations of the library.

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

### Dependency Injection

Create an interface that contains all the necessary methods (both sync and async). The name must be identitical on the purpose of the repository.

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

Then, implement it on the repository.

```csharp
public class CustomerRepository : BaseRepository<Customer, SqlConnection>, ICustomerRepository
{
    ...
}
```

### Service Configuration and Registration

Register it as singleton if you...

- Are using the [ConnectionPersistency.Instance](/enumeration/connectionpersistency#instance) enumeration.
- Enabled the [ICache](/interface/icache) object.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<ICustomerRepository, CustomerRepository>();
}
```

Otherwise, register it as transient.


```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<ICustomerRepository, CustomerRepository>();
}
```

### Key Take-aways

- The transaction argument is needed in every method in order for you to enable the Unit of Work (UOW).
- The cache key argument is needed in the case you need to cache the result.
- The interface is needed for dependency injection.
- The singleton registration is needed for caching and connection persistency.
- The repository must be short and precise on its purpose.