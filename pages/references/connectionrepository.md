---
layout: default
sidebar: references
title: "Connection-Based Repository"
nav_order: 2
permalink: /reference/connectionrepository
tags: [repodb, class, connectionrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: References
---

# Connection-Based Repository

---

This page contains the reference implementation when implementing a repository that is using a connection object per method. The consolidated output of this page can be found [here](/reference/output/connectionrepository).

> This kind of repository is direct and is usually unstructured. Use this repository if you are in minimal development.

#### Recommended Objects (Optional)

- [ICache](/interface/icache)
- [ITrace](/interface/itrace)

#### Recommended Properties (Optional)

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

Below is the sample repository implementation.

For the factory classes.

```csharp
public class NorthwindRepository
{
    private IOptions<AppSettings> _settings;

    public RepositoryBase(IOptions<AppSetting> settings)
    {
        _settings = settings;
        Cache = CacheFactory.CreateCacher();
        Trace = TraceFactory.CreateTracer();
    }

    /*** Properties ***/

    public ITrace Trace { get; }

    public ICache Cache { get; }

    ...
}
```

For the dependency-injected classes.

```csharp
public class NorthwindRepository
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

    ...
}
```

### Operational Methods

Below is the recommended way when exposing a method that return the records.

```csharp
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
```

Below is the recommended way when exposing a method that returns a single record.

```csharp
public Customer GetCustomer(int id)
{
    using (var connection = CreateConnection())
    {
        return connection.Query<Customer>(id,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that deletes a record.

```csharp
public int DeleteCustomer(int id)
{
    using (var connection = CreateConnection())
    {
        return connection.Delete<Customer>(id,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that merges a record.

```csharp
public int MergeCustomer(Customer entity)
{
    using (var connection = CreateConnection())
    {
        return connection.Merge<Customer, int>(entity,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that saves a record.

```csharp
public int SaveCustomer(Customer entity)
{
    using (var connection = CreateConnection())
    {
        return connection.Insert<Customer, int>(entity,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that updates a record.

```csharp
public int UpdateCustomer(Customer entity)
{
    using (var connection = CreateConnection())
    {
        return connection.Update<Customer>(entity,
            trace: Trace);
    }
}
```

### Async Methods

Ensure that all the synchronous methods you had created has the corresponding asynchronous methods suffixed by `Async` keyword. Within these methods, ensure that you are calling the corresponding asynchronous operations of the library.

```csharp
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

public async Task<Customer> GetCustomerAsync(int id)
{
    using (var connection = CreateConnection())
    {
        return await connection.QueryAsync<Customer>(id,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}

// Delete

public async Task<int> DeleteCustomerAsync(int id)
{
    using (var connection = CreateConnection())
    {
        return await connection.DeleteAsync<Customer>(id,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}

// Merge

public async Task<int> MergeCustomerAsync(Customer entity)
{
    using (var connection = CreateConnection())
    {
        return await connection.MergeAsync<Customer, int>(entity,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}

// Save

public async Task<int> SaveCustomerAsync(Customer entity)
{
    using (var connection = CreateConnection())
    {
        return await connection.SaveAsync<Customer, int>(entity,
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
```

### Dependency Injection

Create an interface that contains all the necessary methods. The name must be identitical on the purpose of the repository.

```csharp
public interface INorthwindRepository<TDbConnection>
    where TDbConnection : DbConnection
{
    /*** Helper ***/

    TDbConnection GetConnection();

    /*** Non-Async ***/

    IEnumerable<Customer> GetCustomers(string cacheKey = null);

    Customer GetCustomer(int id);

    int DeleteCustomer(int id);

    int MergeCustomer(Customer entity);

    int SaveCustomer(Customer entity);

    int Update<Customer>(Customer entity);

    /*** Async ***/

    Task<IEnumerable<Customer>> GetCustomersAsync(string cacheKey = null);

    Task<Customer> GetCustomerAsync(int id);

    Task<int> DeleteCustomerAsync(int id);

    Task<objec> MergeCustomerAsync(Customer entity);
    
    Task<int> SaveCustomerAsync(Customer entity);

    Task<int> UpdateCustomerAsync(Customer entity);
}
```

Then, implement it on the repository.

```csharp
public class NorthwindRepository<DbConnection> : INorthwindRepository<DbConnection>
    where TDbConnection : DbConnection
{
    ...
}
```

### Service Configuration and Registration

Register it as singleton if you...

- Enabled the [ICache](/interface/icache) object.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindRepository, NorthwindRepository>();
}
```

Otherwise, register it as transient.


```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

### Key Take-aways

- The async methods must be provided in all methods.
- The repository must be short and precise on its purpose.