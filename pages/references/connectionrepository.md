---
layout: page
title: "Connection Repository Reference (RepoDb)"
permalink: /reference/connectionrepository
tags: [repodb, class, connectionrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Connection Repository

This page contains the reference implementation when implementing a repository that is using a connection object per method.

The consolidated output of this page can be found [here](/reference/output/connectionrepository).

> This kind of repository is usually being created as a base or generic repository that can be used by any models and databases. Imagine that you would like to have a reusable repository in any purpose.

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
public class RepositoryBase<TDbConnection> : IRepository<TDbConnection>
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

    ...
}
```

#### Helper Methods

Create a method that creates a connection object.

```csharp
public TDbConnection CreateConnection()
{
    var connection = Activator.CreateInstance<TDbConnection>();
    connection.ConnectionString = m_settings.ConnectionString;
    return connection.EnsureOpen();
}
```

#### Operational Methods

Below is the recommended way when exposing a method that returns the records.

```csharp
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
```

Below is the recommended way when exposing a method that return single record.

```csharp
public TEntity Get<TEntity>(object id)
{
    using (var connection = CreateConnection())
    {
        return connection.Query<TEntity>(id,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way to delete a record.

```csharp
public int Delete<TEntity>(object id)
{
    using (var connection = CreateConnection())
    {
        return connection.Delete<TEntity>(id,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that merge a record.

```csharp
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
```

Below is the recommended way when exposing a method that save a record.

```csharp
public object Save<TEntity>(TEntity entity,
    IDbTransaction transaction = null)
{
    using (var connection = CreateConnection())
    {
        return connection.Merge<TEntity>(entity,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that update a record.

```csharp
public int Update<TEntity>(TEntity entity,
    IDbTransaction transaction = null)
{
    return Update<TEntity>(entity,
        transaction: transaction,
            trace: Trace);
}
```

#### Async Methods

Ensure that all records you had created has corresponding asynchronous methods with the same standard as synchronous methods.

```csharp
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
        return await connection.MergeAsync<TEntity>(entity,
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
```

#### Enabling for Dependency Injection

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

#### Using the RepositoryBase

Create a class that inherits the repository.

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings)
    { }
}
```

#### Derived Repository Methods

Create a method that calls the base method.

```csharp
public object SaveCustomer(Customer customer)
{
    return base.Save(customer);
}
```

> Do the same calls for the other methods.

#### Unit of Work

Create a method that accepts multiple entities and wrap it all with transaction object. The connection object must be created explicitly.

```csharp
public void SaveCustomerOrder(int customerId,
    Order order,
    OrderDetail orderDetail)
{
    using (var connection = CreateConnection())
    {
        using (var transaction = connection.BeginTransaction())
        {
            // Save the order
            order.CustomerId = customerId;
            var orderId = connection.Save<Order>(order, transaction: transaction);

            // Save the order detail
            orderDetail.OrderId = orderId;
            var orderDetailId = connection.Save<OrderDetail>(orderDetail, transaction: transaction);

            // Commit the transaction
            transaction.Commit();
        }
    }
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
    services.AddSingleton<IRepositoryBase, RepositoryBase>();
}
```

Otherwise, register it as transient.


```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IRepositoryBase, RepositoryBase>();
}
```

#### Key Take-aways

- The transaction argument is needed in every methods in order for you to enable the Unit of Work (UOW).
- The cache key argument is needed in the case you need to cache the result.
- The interface is needed for dependency injection.
- The singleton registration is needed for caching and connection persistency.
- The method names would be targetted to the entity.
- The repository must be short and precise on its purpose.