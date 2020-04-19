---
layout: navpage
sidebar: references
title: "BaseRepository Reference"
permalink: /reference/baserepository
tags: [repodb, class, baserepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# BaseRepository

This page contains the reference implementation when implementing a repository that inherits the [BaseRepository](/class/baserepository) class.

The consolidated output of this page can be found [here](/reference/output/baserepository).

> The [BaseRepository](/class/baserepository) class is only used for implementing an entity-based repository. Imagine that you are only working with `[dbo].[Customer]` table.

###### Recommended Objects (Optional)

- [Cache](/interface/icache)
- [Trace](/interface/itrace)

###### Recommended Properties (Optional)

- [ConnectionPersistency](/enumeration/connectionpersistency)
- [CommandTimeout](https://docs.microsoft.com/en-us/dotnet/api/microsoft.data.sqlclient.sqlcommand.commandtimeout?view=sqlclient-dotnet-core-1.1)

#### Cache Creation

This must be passed in the constructor of the repository.

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

Or, please refer to [ICache](/interface/icache) interface.

#### Trace Creation

This must be passed in the constructor of the repository.

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

Or, please refer to [ITrace](/interface/itrace) interface.

#### IOptions Class (Settings Object)

This must be injected in the constructor of the repository. Please refer to Microsoft [documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1).

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
    public int CacheItemExpiration { get; set; }
}
```

#### Repository Creation

```csharp
public class CustomerRepository : BaseRepository<Customer, SqlConnection>
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

    ...
}
```

#### Methods Creation

Below is the recommended way when exposing a method that returns all records.

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

Below is the recommended way to deletes a record.

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
```

#### Async Methods

Ensure that all records you had created has corresponding asynchronous methods with the same standard as synchronous methods.

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
```

#### Enabling for Dependency Injection

Create an interface that contains all the necessary methods. The name must be identitical on the purpose of the repository.

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

Then implement it on the repository.

```csharp
public class CustomerRepository : BaseRepository<Customer, SqlConnection>, ICustomerRepository
{
    ...
}
```

#### Repository Injection

Register it as singleton if you.

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

#### Key Take-aways

- The transaction argument is needed in every methods in order for you to enable the Unit of Work (UOW).
- The cache key argument is needed in the case you need to cache the result.
- The interface is needed for dependency injection.
- The singleton registration is needed for caching and connection persistency.
- The repository must be short and precise on its purpose.