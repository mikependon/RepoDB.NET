---
layout: navpage
sidebar: features
title: "Caching"
description: "In general terms, a `Cache` is a component that stores an object (or its states) that is accessible for future used. The object that is being stored can be a result of computational, operational, inputs/outputs or analytical operations and calculations."
permalink: /feature/caching
tags: [repodb, class, cache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Caching

In general terms, a `Cache` is a component that stores an object (or its states) that is accessible for future used. The object that is being stored can be a result of computational, operational, inputs/outputs or analytical operations and calculations.

Usually, it is implemented as a 2nd-layer data storage to provide fast accessibility to the requestor of the data. It is by design to prevent frequent access to the underlying data-store, thus helps provide maximum performance to the software.

In this library, by default, the `Cache` is implemented as a storage in computer memory. By nature, it is simply a dictionary object that holds the `Key` that represents as the pointer to the actual `Data` in the cache storage. It is persisting the data in the cache storage for `180` minutes. But, the user can manually set the time of the persistency.

The objects that are not frequently changing but is mostly in used in the application are the candidate for caching.

#### How to use the Cache?

Simply pass a value to the `cacheKey` argument when calling the operation.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var products = connection.QueryAll<Product>(cacheKey: "products");
}
```

#### Selecting the proper cache key
Each cache key should preferably be unique to the query executed, so that different methods don't end up unintentionally sharing data.

Constructing a unique key is left to the developer,
but a good best practice is to adopt a convention for generating the cache keys, such as
`className-methodName-queryArgument1Value-queryArgument2Value` or `entityName-queryArgument1Name-queryArgument1Value--queryArgument1Name-queryArgument2Value` etc.


Following this naming convention makes it easy to examine keys at run-time and establish the source and guarantees uniqueness.
```csharp
// An example of the second cache key convention:
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var productId = 5;
    Query<Product>(product => product.Id == productId,
        cacheKey: $"product-id-{productId}");
}
```


As mentioned, by default the cache is placed in the computer memory via [MemoryCache](/class/memorycache) object. It is a simple dictionary object (key/value pairs).

#### Setting the Cache Expiration

Simply pass a value to the `cacheItemExpiration` argument when calling the operation. This value will be ignored if the `cacheKey` is not provided.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var expirationInMinutes = 60 * 24; // 1 day
    var products = connection.QueryAll<Product>(cacheKey: "products",
        cacheItemExpiration: expirationInMinutes);
}
```

#### Create a Customize Cache Class

Create a class that implements the [ICache](/interface/icache) interface.

```csharp
public class JsonCache : ICache
{
    public JsonCache(string path,
        string extension)
    {
        Path = path;
        Extension = extension;
    }

    /*** Properties ***/

    public string Extension { get; }
    public string Path { get; }

    
    /*** Methods ***/

    public void Add<T>(string key,
        T value,
        int expiration = 180,
        bool throwException = true)
    {
        ...
    }

    public void Add<T>(CacheItem<T> item,
        bool throwException = true)
    {
        ...
    }

    public void Clear()
    {
        ...
    }

    ...
}
```

> You have to implement all the interface methods and manually handle each of them. Please see our [reference implementation](/reference/jsoncache) for more information.

#### Using a Cache in the Connection

Simply pass the `cacheKey` and `cache` object when calling the operation.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var products = connection.QueryAll<Product>(cacheKey: "AllProducts", cache: new JsonCache());
}
```

#### Injecting the Cache in the Repository

Simply inject it in the contructor. Below is a sample code for [BaseRepository](/class/baserepository) class.

```csharp
// Repository
public class CustomerRepository : BaseRepository<Customer, SqlConnection>
{
    public CustomerRepository(IOptions<AppSettings> settings)
        : base(settings.ConnectionString, new JsonCache())
    { }

    ...
}

// Use it like this (or via DI)
using (var repository = new CustomerRepository(settings))
{
    ...
}
```

And below is for [DbRepository](/class/dbrepository) class.

```csharp
// Repository
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings)
        : base(settings.ConnectionString, new JsonCache())
    { }

    ...
}

// Use it like this (or via DI)
using (var repository = new NorthwindRepository(settings))
{
    ...
}
```

Or via direct class instantiation.

```csharp
// Direct class instantiation of DbRepository
using (var repository = new DbRepository<SqlConnection>(settings.ConnectionString, new JsonCache()))
{
    ...
}
```

#### Create a Cache Factory

Creating a cache `Factory` class is a good way to abstract and ensure single instance of cache object is being created. It is our recommendation when using the cache feature.

The code below ensures that only a single instance of cache object is being used all throughout the application.

```csharp
public static class CacheFactory
{
    private static object m_syncLock = new object();
    private static ICache m_trace = null;
    
    public static ICache CreateCacher()
    {
        if (m_trace == null)
        {
            lock (m_syncLock)
            {
                if (m_trace == null)
                {
                    m_trace = new JsonCache();
                }
            }
        }
        return m_trace;
    }
}
```

And use it in the `IDbConnection` object like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var products = connection.QueryAll<Product>cacheKey: "AllProducts", cache: CacheFactory.CreateCacher());
}
```

Or via repositories.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings)
        : base(settings.ConnectionString, CacheFactory.CreateCacher())
    { }

    ...
}
```

#### Dependency Injection Implementation

Create a custom interface that implements the [ICache](/interface/icache) interface.

```csharp
public interface IJsonCache : ICache
{
    // More custom methods
}
```

Then implement it in the cache class.

```csharp
public class JsonCache : IJsonCache
{
    ...
}
```

Lastly, register in the services collection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<IJsonCache, JsonCache>();
}
```

Below is the code on how to inject it in the repositories.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings,
        IJsonCache cache) // Injected
        : base(settings.ConnectionString, cache)
    { }

    ...
}
```