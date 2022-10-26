---
layout: page
title: DbRepository
permalink: /reference/output/dbrepository
tags: [repodb, dbrepository]
parent: OUTPUT
grand_parent: REFERENCES
---

# DbRepository

---

This page has the consolidated code of the [DbRepository](/reference/dbrepository) reference implementation.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>, INorthwindRepository
{
    // Factory Classes
    public NorthwindRepository(IOptions<AppSetting> settings)
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
    { }
    */

    /*** Sync ***/

    // Get (Many)

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

    public IEnumerable<Order> GetCustomerOrders(int customerId,
        IDbTransaction transaction = null)
    {
        return QueryAll<Order>(o => o.CustomerId == customerId,
            transaction: transaction);
    }

    // Get

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

    // Delete

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

    // Merge

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

    // Save

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

    // Update

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
    
    /*** Async ***/

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
}
```

### Interface

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

### Settings

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
    public int CacheItemExpiration { get; set; }
}
```

### Dependency Injections

For singleton.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindRepository, NorthwindRepository>();
}
```

Or for transient.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

### Cache

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

### Trace

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
