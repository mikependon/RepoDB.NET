---
layout: page
title: "DbRepository Reference Output (RepoDb)"
permalink: /reference/output/dbrepository
tags: [repodb, class, dbrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

#### NorthwindRepository (DbRepository)

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings.ConnectionString,
            commandTimeout: settings.CommandTimeout,
            connectionPersistency: ConnectionPersistency.PerCall,
            cache: CacheFactory.CreateCacher(),
            cacheItemExpiration: settings.CacheItemExpiration,
            trace: TraceFactory.CreateTracer(),
            statementBuilder: null /* Not necessary */ )
    { }

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

    // Save

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
}
```

#### Settings

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
    public int CacheItemExpiration { get; set; }
}
```

#### Dependency Injection (Normal)

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

#### Dependency Injection (with Cache or ConnectionPersistency.Instace)

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindRepository, NorthwindRepository>();
}
```

#### CacheFactory

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

#### TraceFactory

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
