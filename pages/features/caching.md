---
layout: default
sidebar: features
title: "Caching"
description: "In general terms, a `Cache` is a component that stores an object (or its states) in any form of temporary storage that is accessible for future used. The object that is being stored can be a result of computational, operational, inputs/outputs or analytical operations and calculations."
permalink: /feature/caching
tags: [repodb, cache]
parent: FEATURES
---

# Caching

---

A cache is a component that stores an object (or its state) in temporary storage for future use. The stored object can be the result of computational, operational, I/O, or analytical operations.

Caching is typically implemented as a second-layer data store to provide fast data access, reducing frequent calls to the underlying data store and improving application performance.

The diagram below shows the caching implementation in this library.

<img src="../../assets/images/site/cache.svg" />

By default, caching is backed by in-memory storage through the [MemoryCache](/class/memorycache) object — a simple dictionary that maps keys to cached data. Cache entries persist for 180 minutes by default, but the expiration can be set explicitly during each call.

{: .important }
> Database tables that change infrequently but are frequently read are good candidates for caching.

## How to use the Cache?

Pass a literal string to the `cacheKey` argument when calling the operation.

When using the connection object directly, an [ICache](/interface/icache) instance must be passed explicitly to the `cache` argument.

```csharp
var cache = CacheFactory.GetMemoryCache();
using (var connection = new SqlConnection(connectionString))
{
    var products = connection.QueryAll<Product>(cacheKey: "products", cache: cache);
}
```

When using [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository), the cache instance is managed by the repository and does not need to be passed on each call.

```csharp
var cache = CacheFactory.GetMemoryCache();
using (var repository = new DbRepository<Product, SqlConnection>(connectionString, cache))
{
    var products = repository.QueryAll(cacheKey: "products");
}
```

{: .important }
> Use [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository) when you prefer not to manage the cache object manually.

## Selecting a Proper Cache Key

Each cache key should be unique to the query it represents, so that different methods do not unintentionally share the same cached data.

Key construction is left to the developer, but a consistent naming convention is recommended:

- Class Name + Property Name + Query Argument1 + Query Argument2 (Product-Name-Chocolate-White)
- Entity Name + Argument1 Name + Argument2 Name + Argument1Value + Argument2Value (Product-Name-Color--Chocolate-White)

A consistent convention makes it easy to inspect keys at runtime and determine their source. It also guarantees uniqueness to avoid cache collisions.

```csharp
// An example of the second cache key convention:
var cache = CacheFactory.GetMemoryCache();
using (var connection = new SqlConnection(connectionString))
{
    var productId = 5;
    var product = connection.Query<Product>(product => product.Id == productId,
        cacheKey: $"Product-Id-{productId}", cache: cache);
}
```

By default, the cache is stored in memory via the [MemoryCache](/class/memorycache) object — a simple key/value dictionary.

## Setting the Cache Expiration

Pass a value to the `cacheItemExpiration` argument when calling the operation. This value is ignored if `cacheKey` is not provided.

```csharp
var cache = CacheFactory.GetMemoryCache();
using (var connection = new SqlConnection(connectionString))
{
    var expirationInMinutes = 60 * 24; // 1 day
    var products = connection.QueryAll<Product>(cacheKey: "products",
        cacheItemExpiration: expirationInMinutes, cache: cache);
}
```

## Removing the Cache Item

Use the `Remove()` method of the [ICache](/interface/icache) interface to remove a cache entry.

```csharp
var cache = CacheFactory.GetMemoryCache();
using (var connection = new SqlConnection(connectionString))
{
    var products = connection.QueryAll<Product>(cacheKey: "products", cache: cache);
    cache.Remove("products");
}
```

Alternatively, set the `Expiration` property to force expiration.

```csharp
var cache = CacheFactory.GetMemoryCache();
using (var connection = new SqlConnection(connectionString))
{
    var products = connection.QueryAll<Product>(cacheKey: "products", cache: cache);
    var item = cache.Get<Product>("products");
    item.Expiration = DateTime.UtcNow.AddSecond(-1);
}
```

When using [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository), use the `Cache` property directly.

```csharp
using (var repository = new DbRepository<Product, SqlConnection>(connectionString))
{
    var products = repository.QueryAll(cacheKey: "products");
    repository.Cache.Remove("products");
}
```

## Create a Customize Cache Class

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

{: .note }
> All interface methods must be implemented and handled manually.

## Injecting the Cache in the Repository

Inject the cache in the constructor. The following is a sample for the [BaseRepository](/class/baserepository) class.

```csharp
// Repository
public class CustomerRepository : BaseRepository<Customer, SqlConnection>
{
    public CustomerRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString, new JsonCache())
    { }

    ...
}

// Use it like this (or via DI)
using (var repository = new CustomerRepository(settings))
{
    ...
}
```

For the [DbRepository](/class/dbrepository) class:

```csharp
// Repository
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString, new JsonCache())
    { }

    ...
}

// Use it like this (or via DI)
using (var repository = new NorthwindRepository(settings))
{
    ...
}
```

Or via direct class instantiation:

```csharp
// Direct class instantiation of DbRepository
using (var repository = new DbRepository<SqlConnection>(settings.Value.ConnectionString, new JsonCache()))
{
    ...
}
```

## Dependency Injection Implementation

Create a custom interface that extends [ICache](/interface/icache).

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

Register it in the services collection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<IJsonCache, JsonCache>();
}
```

Inject it into the repository constructor.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings,
        IJsonCache cache) // Injected
        : base(settings.Value.ConnectionString, cache)
    { }

    ...
}
```

## Create a Cache Factory

If dependency injection is not preferred, a cache factory class ensures a single cache instance is used throughout the application.

```csharp
public static class CacheFactory
{
    private readonly static object syncLock;
    private static ICache jsonCache = null;

    static CacheFactory()
    {
        syncLock = new object();
    }

    public static ICache GetJsonCache()
    {
        if (jsonCache == null)
        {
            lock (syncLock)
            {
                if (jsonCache == null)
                {
                    jsonCache = new JsonCache();
                }
            }
        }
        return jsonCache;
    }
}
```

Use it with a connection object:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var products = connection.QueryAll<Product>cacheKey: "AllProducts", cache: CacheFactory.GetJsonCache());
}
```

Or via a repository:

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString, CacheFactory.GetJsonCache())
    { }

    ...
}
```

{: .note }
> Please visit our [JSON Cache](/reference/jsoncache) reference implementation page to get more insights on how to implement a file-based caching object using JSON.
