---
layout: default
sidebar: references
title: DbRepository
nav_order: 3
permalink: /reference/dbrepository
tags: [repodb, class, dbrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: References
---

# DbRepository

---

This page contains the reference implementation when implementing a repository that inherits the [DbRepository](/class/dbrepository) class. The consolidated output of this page can be found [here](/reference/output/dbrepository).

> The [DbRepository](/class/dbrepository) class is used for implementing a shared repository. Imagine you are working with `[dbo].[Customer]`, `[dbo].[Product]` and `[dbo].[Order]` tables.

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

Simply inherit the [DbRepository](/class/dbrepository) class and pass a generic type for the connection object.

Below is the sample repository implementation.

For the factory classes.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings.Value.ConnectionString,
            commandTimeout: settings.CommandTimeout,
            connectionPersistency: ConnectionPersistency.PerCall,
            cache: CacheFactory.CreateCacher(),
            cacheItemExpiration: settings.CacheItemExpiration,
            trace: TraceFactory.CreateTracer(),
            statementBuilder: null)
    {}

    ...
}
```

For the dependency-injected classes.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
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
    {}

    ...
}
```

### Methods

Below is the recommended way when exposing a method that returns all the records.

```csharp
public IEnumerable<Customer> GetCustomers(string cacheKey = null,
    IDbTransaction transaction = null)
{
    return QueryAll<Customer>(cacheKey: cacheKey,
        transaction: transaction);
}

public IEnumerable<Product> GetProducts(string cacheKey = null,
    IDbTransaction transaction = null)
{
    return QueryAll<Product>(cacheKey: cacheKey,
        transaction: transaction);
}
```

Below is the recommended way when exposing a method that return the records related to a parent entity.

```csharp
public IEnumerable<Order> GetCustomerOrders(int customerId,
    IDbTransaction transaction = null)
{
    return QueryAll<Order>(o => o.CustomerId == customerId,
        transaction: transaction);
}
```

Below is the recommended way when exposing a method that returns a single record.

```csharp
public Customer GetCustomer(int id,
    string cacheKey = null,
    IDbTransaction transaction = null)
{
    return Query<Customer>(id,
        cacheKey: cacheKey,
        transaction: transaction).FirstOrDefault();
}

public Product GetProduct(int id,
    string cacheKey = null,
    IDbTransaction transaction = null)
{
    return Query<Product>(p => p.Name == name,
        cacheKey: cacheKey,
        transaction: transaction).FirstOrDefault();
}

public Order GetOrder(int id,
    IDbTransaction transaction = null)
{
    return Query<Order>(id,
        transaction: transaction).FirstOrDefault();
}
```

Below is the recommended way when exposing a method that deletes a record.

```csharp
public int DeleteOrder(int id,
    IDbTransaction transaction = null)
{
    return Delete<Order>(id,
        transaction: transaction);
}

public int DeleteProduct(int id,
    IDbTransaction transaction = null)
{
    return Delete<Product>(id,
        transaction: transaction);
}
```

Below is the recommended way to deletes a record with children.

```csharp
public int DeleteCustomer(int id,
    IDbTransaction transaction = null)
{
    // Delete the child orders first
    var deletedOrders = Delete<Order>(o => o.CustomerId == id,
        transaction: transaction);

    // No need to check (if deletedOrders > 0) as some customers does not have orders
    return Delete<Customer>(id,
        transaction: transaction);
}
```

We suggest that you check the presence of the transaction object.

```csharp
if (transaction == null)
{
    var connection = CreateConnection();
    try
    {
        using (var implicitTransaction = connection.BeginTransaction())
        {
            return DeleteCustomer(id, implicitTransaction);
        }
    }
    finally
    {
        if (ConnectionPersistency == ConnectionPersistency.PerCall)
        {
            connection.Dispose();
        }
    }
}
```

Below is the recommended way when exposing a method that merges a record.

```csharp
public object MergeCustomer(Customer customer,
    IDbTransaction transaction = null)
{
    return Merge<Customer>(customer,
        transaction: transaction);
}

public object MergeOrder(Order order,
    IDbTransaction transaction = null)
{
    return Merge<Order>(order,
        transaction: transaction);
}

public object MergeProduct(Product product,
    IDbTransaction transaction = null)
{
    return Merge<Product>(product,
        transaction: transaction);
}
```

Below is the recommended way when exposing a method that saves a record.

```csharp
public object SaveCustomer(Customer customer,
    IDbTransaction transaction = null)
{
    return Insert<Customer>(customer,
        transaction: transaction);
}

public object SaveOrder(Order order,
    IDbTransaction transaction = null)
{
    return Insert<Order>(order,
        transaction: transaction);
}

public object SaveProduct(Product product,
    IDbTransaction transaction = null)
{
    return Insert<Product>(product,
        transaction: transaction);
}
```

Below is the recommended way when exposing a method that updates a record.

```csharp
public int UpdateCustomer(Customer customer,
    IDbTransaction transaction = null)
{
    return Update<Customer>(customer,
        transaction: transaction);
}

public int UpdateOrder(Order order,
    IDbTransaction transaction = null)
{
    return Update<Order>(order,
        transaction: transaction);
}

public int UpdateProduct(Product product,
    IDbTransaction transaction = null)
{
    return Update<Product>(product,
        transaction: transaction);
}
```

### Async Methods

Ensure that all the synchronous methods you had created has the corresponding asynchronous methods suffixed by `Async` keyword. Within these methods, ensure that you are calling the corresponding asynchronous operations of the library.

```csharp
// Get (Many)

public async Task<IEnumerable<Customer>> GetCustomersyAsync(string cacheKey = null,
    IDbTransaction transaction = null)
{
    return await QueryAllAsync<Customer>(cacheKey: cacheKey,
        transaction: transaction);
}

public async Task<IEnumerable<Product>> GetProductsyAsync(string cacheKey = null,
    IDbTransaction transaction = null)
{
    return await QueryAllAsync<Product>(cacheKey: cacheKey,
        transaction: transaction);
}

public async Task<IEnumerable<Order>> GetCustomerOrdersyAsync(int customerId,
    IDbTransaction transaction = null)
{
    return await QueryAllAsync<Order>(o => o.CustomerId == customerId,
        transaction: transaction);
}

public async Task<Customer> GetCustomeryAsync(int id,
    string cacheKey = null,
    IDbTransaction transaction = null)
{
    return (await QueryAsync<Customer>(id,
        cacheKey: cacheKey,
        transaction: transaction)).FirstOrDefault();
}

// Get

public async Task<Product> GetProductyAsync(int id,
    string cacheKey = null,
    IDbTransaction transaction = null)
{
    return await QueryAsync<Product>(p => p.Name == name,
        cacheKey: cacheKey,
        transaction: transaction).FirstOrDefault();
}

public async Task<Order> GetOrderyAsync(int id,
    IDbTransaction transaction = null)
{
    return await QueryAsync<Order>(id,
        transaction: transaction).FirstOrDefault();
}

// Delete

public async Task<int> DeleteOrderyAsync(int id,
    IDbTransaction transaction = null)
{
    return await DeleteAsync<Order>(id,
        transaction: transaction);
}

public async Task<int> DeleteProductyAsync(int id,
    IDbTransaction transaction = null)
{
    return await DeleteAsync<Product>(id,
        transaction: transaction);
}

public async Task<int> DeleteCustomeryAsync(int id,
    IDbTransaction transaction = null)
{
    // Delete the child orders first
    var deletedOrders = await DeleteAsync<Order>(o => o.CustomerId == id,
        transaction: transaction);

    // No need to check (if deletedOrders > 0) as some customers does not have orders
    return await DeleteAsync<Customer>(id,
        transaction: transaction);
}

// Merge

public async Task<object> MergeCustomeryAsync(Customer customer,
    IDbTransaction transaction = null)
{
    return await MergeAsync<Customer>(customer,
        transaction: transaction);
}

public async Task<object> MergeOrderyAsync(Order order,
    IDbTransaction transaction = null)
{
    return await MergeAsync<Order>(order,
        transaction: transaction);
}

public async Task<object> MergeProductyAsync(Product product,
    IDbTransaction transaction = null)
{
    return await MergeAsync<Product>(product,
        transaction: transaction);
}

// Save

public async Task<object> SaveCustomeryAsync(Customer customer,
    IDbTransaction transaction = null)
{
    return await InsertAsync<Customer>(customer,
        transaction: transaction);
}

public async Task<object> SaveOrderyAsync(Order order,
    IDbTransaction transaction = null)
{
    return await InsertAsync<Order>(order,
        transaction: transaction);
}

public async Task<object> SaveProductyAsync(Product product,
    IDbTransaction transaction = null)
{
    return await InsertAsync<Product>(product,
        transaction: transaction);
}

// Update

public async Task<int> UpdateCustomeryAsync(Customer customer,
    IDbTransaction transaction = null)
{
    return await UpdateAsync<Customer>(customer,
        transaction: transaction);
}

public async Task<int> UpdateOrderyAsync(Order order,
    IDbTransaction transaction = null)
{
    return await UpdateAsync<Order>(order,
        transaction: transaction);
}

public async Task<int> UpdateProductyAsync(Product product,
    IDbTransaction transaction = null)
{
    return await UpdateAsync<Product>(product,
        transaction: transaction);
}
```

### Dependency Injection

Create an interface that contains all the necessary methods. The name must be identitical on the purpose of the repository.

```csharp
public interface INorthwindRepository
{
    /*** Non-Async ***/

    // Get (Many)

    IEnumerable<Customer> GetCustomers(string cacheKey = null,
        IDbTransaction transaction = null);

    IEnumerable<Product> GetProducts(string cacheKey = null,
        IDbTransaction transaction = null);

    IEnumerable<Order> GetCustomerOrders(int customerId,
        IDbTransaction transaction = null);

    // Get

    Customer GetCustomer(int id,
        string cacheKey = null,
        IDbTransaction transaction = null);

    Product GetProduct(int id,
        string cacheKey = null,
        IDbTransaction transaction = null);

    Order GetOrder(int id,
        IDbTransaction transaction = null);

    // Delete

    int DeleteOrder(int id,
        IDbTransaction transaction = null);

    int DeleteProduct(int id,
        IDbTransaction transaction = null);

    int DeleteCustomer(int id,
        IDbTransaction transaction = null);

    // Merge

    object MergeCustomer(Customer customer,
        IDbTransaction transaction = null);

    object MergeOrder(Order order,
        IDbTransaction transaction = null);

    object MergeProduct(Product product,
        IDbTransaction transaction = null);

    // Save

    object SaveCustomer(Customer customer,
        IDbTransaction transaction = null);

    object SaveOrder(Order order,
        IDbTransaction transaction = null);

    object SaveProduct(Product product,
        IDbTransaction transaction = null);

    // Update

    int UpdateCustomer(Customer customer,
        IDbTransaction transaction = null);

    int UpdateOrder(Order order,
        IDbTransaction transaction = null);

    int UpdateProduct(Product product,
        IDbTransaction transaction = null);

    /*** Async ***/

    // Get (Many)

    Task<IEnumerable<Customer>> GetCustomersyAsync(string cacheKey = null,
        IDbTransaction transaction = null);

    Task<IEnumerable<Product>> GetProductsyAsync(string cacheKey = null,
        IDbTransaction transaction = null);

    Task<IEnumerable<Order>> GetCustomerOrdersyAsync(int customerId,
        IDbTransaction transaction = null);

    Task<Customer> GetCustomeryAsync(int id,
        string cacheKey = null,
        IDbTransaction transaction = null);

    // Get

    Task<Product> GetProductyAsync(int id,
        string cacheKey = null,
        IDbTransaction transaction = null);

    Task<Order> GetOrderyAsync(int id,
        IDbTransaction transaction = null);

    // Delete

    Task<int> DeleteOrderyAsync(int id,
        IDbTransaction transaction = null);

    Task<int> DeleteProductyAsync(int id,
        IDbTransaction transaction = null);

    Task<int> DeleteCustomeryAsync(int id,
        IDbTransaction transaction = null);

    // Merge

    Task<object> MergeCustomeryAsync(Customer customer,
        IDbTransaction transaction = null);

    Task<object> MergeOrderyAsync(Order order,
        IDbTransaction transaction = null);

    Task<object> MergeProductyAsync(Product product,
        IDbTransaction transaction = null);

    // Save

    Task<object> SaveCustomeryAsync(Customer customer,
        IDbTransaction transaction = null);

    Task<object> SaveOrderyAsync(Order order,
        IDbTransaction transaction = null);

    Task<object> SaveProductyAsync(Product product,
        IDbTransaction transaction = null);

    // Update

    Task<int> UpdateCustomeryAsync(Customer customer,
        IDbTransaction transaction = null);

    Task<int> UpdateOrderyAsync(Order order,
        IDbTransaction transaction = null);

    Task<int> UpdateProductyAsync(Product product,
        IDbTransaction transaction = null);
}
```

Then implement it on the repository.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>, INorthwindRepository
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

- The transaction argument is needed in every method in order for you to enable the Unit of Work (UOW).
- The cache key argument is needed in the case you need to cache the result.
- The interface is needed for dependency injection.
- The singleton registration is needed for caching and connection persistency.
- The method names would be targeted to the entity.
- The repository must be short and precise on its purpose.