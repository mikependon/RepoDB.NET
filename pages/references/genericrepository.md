---
layout: default
sidebar: references
title: "Generic Repository"
nav_order: 4
permalink: /reference/genericrepository
tags: [repodb, class, genericrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: References
---

# Generic Repository

---

This page contains the reference implementation when implementing a generic repository that can be used by any entity model. The consolidated output of this page can be found [here](/reference/output/genericrepository).

> This kind of repository is usually being created as a base repository that can be used by any entity model. Imagine that you would like to have a reusable repository in any purpose. This works like [DbRepository](/class/dbrepository).

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

```csharp
public class RepositoryBase<TDbConnection>
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

    ...
}
```

### Helper Methods

Create a method that creates a connection object.

```csharp
public TDbConnection CreateConnection()
{
    var connection = Activator.CreateInstance<TDbConnection>();
    connection.ConnectionString = _settings.Value.ConnectionString;
    return connection;
}
```

### Operational Methods

Below is the recommended way when exposing a method that return the records.

```csharp
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
```

Below is the recommended way when exposing a method that returns a single record.

```csharp
public TEntity Get<TEntity>(object id)
{
    using (var connection = CreateConnection())
    {
        return connection.Query<TEntity>(id,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that deletes a record.

```csharp
public int Delete<TEntity>(object id)
{
    using (var connection = CreateConnection())
    {
        return connection.Delete<TEntity>(id,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that merges a record.

```csharp
public object Merge<TEntity>(TEntity entity)
{
    using (var connection = CreateConnection())
    {
        return connection.Merge<TEntity>(entity,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that saves a record.

```csharp
public object Save<TEntity>(TEntity entity)
{
    using (var connection = CreateConnection())
    {
        return connection.Insert<TEntity>(entity,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that updates a record.

```csharp
public int Update<TEntity>(TEntity entity)
{
    using (var connection = CreateConnection())
    {
        return Update<TEntity>(entity,
            trace: Trace);
    }
}
```

### Async Methods

Ensure that all the synchronous methods you had created has the corresponding asynchronous methods suffixed by `Async` keyword. Within these methods, ensure that you are calling the corresponding asynchronous operations of the library.

```csharp
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

public async Task<TEntity> GetAsync<TEntity>(object id)
{
    using (var connection = CreateConnection())
    {
        return await connection.QueryAsync<TEntity>(id,
            commandTimeout: _settings.CommandTimeout,
            trace: Trace);
    }
}

// Delete

public async Task<int> DeleteAsync<TEntity>(object id)
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
        return await connection.InsertAsync<TEntity>(entity,
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
```

### Dependency Injection

Create an interface that contains all the necessary methods. The name must be identitical on the purpose of the repository.

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

Then implement it on the repository.

```csharp
public class RepositoryBase<DbConnection> : IRepositoryBase<DbConnection>
    where TDbConnection : DbConnection
{
    ...
}
```

### Using the RepositoryBase

Create a class that inherits the repository.

For the factory classes.

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings)
    { }
}
```

For the dependency-injected classes.

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSetting> settings,
        ICache cache,
        ITrace trace)
        : base(settings,
            cache,
            trace)
    { }
}
```

### Derived Repository Methods

Create a method that calls the base method. Below is the recommended way for retrieving a single record.

```csharp
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
```

Below is the recommended way for saving a record.

```csharp
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
```

> Do the same calls for the other methods. Also, please take note to always implement the corresponding asynchronous methods.

### Interface for Derived Repository

Implement the interface for the derived repository.

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

### Derived Interface Implementation

Create a class that inherits the repository and implements the interface.

For the factory classes.

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings)
    { }
}
```

For the dependency-injected classes.

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(IOptions<AppSetting> settings,
        ICache cache,
        ITrace trace)
        : base(settings,
            cache,
            trace)
    { }
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

- The dependency injection must be happened only in the derived repositories.
- The async methods must be provided in all methods.
- The repository must be short and precise on its purpose.