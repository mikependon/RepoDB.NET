---
layout: default
sidebar: references
title: DbRepository
nav_order: 3
permalink: /reference/dbrepository
tags: [repodb, dbrepository]
parent: REFERENCES
---

# DbRepository

---

This page contains the reference implementation for a repository that inherits the [DbRepository](/class/dbrepository) class. The consolidated output can be found [here](/reference/output/dbrepository).

{: .important }
> The [DbRepository](/class/dbrepository) class is intended for shared repositories that span multiple tables, such as `[dbo].[Customer]`, `[dbo].[Product]`, and `[dbo].[Order]`.

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

Inherit the [DbRepository](/class/dbrepository) class and provide the connection object as a generic type argument.

Using factory classes:

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

Using dependency injection:

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

## Methods

Recommended implementation for methods that return all records:

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

Recommended implementation for a method that returns records related to a parent entity:

```csharp
public IEnumerable<Order> GetCustomerOrders(int customerId,
    IDbTransaction transaction = null)
{
    return QueryAll<Order>(o => o.CustomerId == customerId,
        transaction: transaction);
}
```

Recommended implementation for methods that return a single record:

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

Recommended implementation for methods that delete a record:

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

Recommended implementation for deleting a record with child records:

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

It is recommended to check for an active transaction object:

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

Recommended implementation for methods that merge a record:

```csharp
public int MergeCustomer(Customer customer,
    IDbTransaction transaction = null)
{
    return Merge<Customer, int>(customer,
        transaction: transaction);
}

public int MergeOrder(Order order,
    IDbTransaction transaction = null)
{
    return Merge<Order, int>(order,
        transaction: transaction);
}

public int MergeProduct(Product product,
    IDbTransaction transaction = null)
{
    return Merge<Product, int>(product,
        transaction: transaction);
}
```

Recommended implementation for methods that save a record:

```csharp
public int SaveCustomer(Customer customer,
    IDbTransaction transaction = null)
{
    return Insert<Customer, int>(customer,
        transaction: transaction);
}

public int SaveOrder(Order order,
    IDbTransaction transaction = null)
{
    return Insert<Order, int>(order,
        transaction: transaction);
}

public int SaveProduct(Product product,
    IDbTransaction transaction = null)
{
    return Insert<Product, int>(product,
        transaction: transaction);
}
```

Recommended implementation for methods that update a record:

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

## Async Methods

Each synchronous method must have a corresponding asynchronous counterpart with the `Async` suffix, delegating to the library's async operations.

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

public async Task<int> MergeCustomeryAsync(Customer customer,
    IDbTransaction transaction = null)
{
    return await MergeAsync<Customer,int>(customer,
        transaction: transaction);
}

public async Task<int> MergeOrderyAsync(Order order,
    IDbTransaction transaction = null)
{
    return await MergeAsync<Order, int>(order,
        transaction: transaction);
}

public async Task<int> MergeProductyAsync(Product product,
    IDbTransaction transaction = null)
{
    return await MergeAsync<Product, int>(product,
        transaction: transaction);
}

// Save

public async Task<int> SaveCustomerAsync(Customer customer,
    IDbTransaction transaction = null)
{
    return await InsertAsync<Customer, int>(customer,
        transaction: transaction);
}

public async Task<int> SaveOrderAsync(Order order,
    IDbTransaction transaction = null)
{
    return await InsertAsync<Order, int>(order,
        transaction: transaction);
}

public async Task<int> SaveProductAsync(Product product,
    IDbTransaction transaction = null)
{
    return await InsertAsync<Product, int>(product,
        transaction: transaction);
}

// Update

public async Task<int> UpdateCustomerAsync(Customer customer,
    IDbTransaction transaction = null)
{
    return await UpdateAsync<Customer>(customer,
        transaction: transaction);
}

public async Task<int> UpdateOrderAsync(Order order,
    IDbTransaction transaction = null)
{
    return await UpdateAsync<Order>(order,
        transaction: transaction);
}

public async Task<int> UpdateProductAsync(Product product,
    IDbTransaction transaction = null)
{
    return await UpdateAsync<Product>(product,
        transaction: transaction);
}
```

## Dependency Injection

Create an interface covering all required methods. Name it to reflect the repository's purpose.

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

    int MergeCustomer(Customer customer,
        IDbTransaction transaction = null);

    int MergeOrder(Order order,
        IDbTransaction transaction = null);

    int MergeProduct(Product product,
        IDbTransaction transaction = null);

    // Save

    int SaveCustomer(Customer customer,
        IDbTransaction transaction = null);

    int SaveOrder(Order order,
        IDbTransaction transaction = null);

    int SaveProduct(Product product,
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

    Task<int> MergeCustomeryAsync(Customer customer,
        IDbTransaction transaction = null);

    Task<int> MergeOrderyAsync(Order order,
        IDbTransaction transaction = null);

    Task<int> MergeProductyAsync(Product product,
        IDbTransaction transaction = null);

    // Save

    Task<int> SaveCustomerAsync(Customer customer,
        IDbTransaction transaction = null);

    Task<int> SaveOrderAsync(Order order,
        IDbTransaction transaction = null);

    Task<int> SaveProductAsync(Product product,
        IDbTransaction transaction = null);

    // Update

    Task<int> UpdateCustomerAsync(Customer customer,
        IDbTransaction transaction = null);

    Task<int> UpdateOrderAsync(Order order,
        IDbTransaction transaction = null);

    Task<int> UpdateProductAsync(Product product,
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

## Service Configuration and Registration

Register as singleton when:

- Using the [ConnectionPersistency.Instance](/enumeration/connectionpersistency#instance) enumeration.
- [ICache](/interface/icache) is enabled.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindRepository, NorthwindRepository>();
}
```

Otherwise, register as transient.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

## Key Take-aways

- Pass the transaction argument in every method to support the Unit of Work (UOW) pattern.
- Pass the cache key argument to enable result caching.
- Define an interface to support dependency injection.
- Register as singleton when caching or connection persistency is required.
- Name methods after the target entity.
- Keep each repository focused on a single, well-defined purpose.