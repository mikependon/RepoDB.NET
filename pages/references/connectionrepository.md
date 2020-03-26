---
layout: page
title: "Connection-Based Repository Reference (RepoDb)"
permalink: /reference/connectionrepository
tags: [repodb, class, connectionrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Connection-Based Repository

This page contains the reference implementation when implementing a repository that is using a connection object per method.

The consolidated output of this page can be found [here](/reference/output/connectionrepository).

> This kind of repository is direct and is usually unstructured. Use this repository if you are in minimal development.

###### Recommended Objects (Optional)

- [Cache](/interface/icache)
- [Trace](/interface/itrace)

###### Recommended Properties (Optional)

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
public class NorthwindRepository
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

#### Operational Methods

Below is the recommended way when exposing a method that returns the records.

```csharp
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
```

Below is the recommended way when exposing a method that returns a single record.

```csharp
public Customer GetCustomer(object id)
{
    using (var connection = CreateConnection())
    {
        return connection.Query<Customer>(id,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way to deletes a record.

```csharp
public int DeleteCustomer(object id)
{
    using (var connection = CreateConnection())
    {
        return connection.Delete<Customer>(id,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}
```

Below is the recommended way when exposing a method that merges a record.

```csharp
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
```

Below is the recommended way when exposing a method that saves a record.

```csharp
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
```

Below is the recommended way when exposing a method that updates a record.

```csharp
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
```

#### Async Methods

Ensure that all records you had created has corresponding asynchronous methods with the same standard as synchronous methods.

```csharp
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

public async Task<Customer> GetCustomerAsync(object id)
{
    using (var connection = CreateConnection())
    {
        return await connection.QueryAsync<Customer>(id,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}

// Delete

public async Task<int> DeleteCustomerAsync(object id)
{
    using (var connection = CreateConnection())
    {
        return await connection.DeleteAsync<Customer>(id,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}

// Merge

public async Task<objec> MergeCustomerAsync(Customer entity)
{
    using (var connection = CreateConnection())
    {
        return await connection.MergeAsync<Customer>(entity,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}

// Save

public async Task<object> SaveCustomerAsync(Customer entity)
{
    using (var connection = CreateConnection())
    {
        return await connection.SaveAsync<Customer>(entity,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}

// Update

public async Task<int> UpdateCustomerAsync(Customer entity)
{
    using (var connection = CreateConnection())
    {
        return await connection.UpdateAsync<Customer>(entity,
            commandTimeout: m_settings.CommandTimeout,
            trace: Trace);
    }
}
```

#### Enabling for Dependency Injection

Create an interface that contains all the necessary methods. The name must be identitical on the purpose of the repository.

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

Then implement it on the repository.

```csharp
public class NorthwindRepository<DbConnection> : INorthwindRepository<DbConnection>
    where TDbConnection : DbConnection
{
    ...
}
```

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

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindRepository, NorthwindRepository>();
}
```

#### Key Take-aways

- The transaction object is needed to passed in all operation for the unit-of-work activity.
- The async methods must be provided in all methods.
- The repository must be short and precise on its purpose.