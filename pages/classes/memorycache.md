---
layout: default
sidebar: classes
title: "MemoryCache"
description: "An advance class that is being used to cache an object into a memory within the library."
permalink: /class/memorycache
tags: [repodb, memorycache]
parent: CLASSES
---

# MemoryCache

---

This class caches objects in memory, eliminating database round-trips for up to 180 minutes (configurable). It implements the [ICache](/interface/icache) interface.

{: .important }
> This is the default cache implementation used by the library.

## Use-Cases

Use this class to cache objects fetched from the database in memory.

## Usability

Pass an instance to the constructor of [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository), or to fetch operations such as [Query](/operation/query) and [QueryAll](/operation/queryall).

## Customize

Define a custom interface that extends [ICache](/interface/icache).

```csharp
public interface IJsonCache : ICache
{
    // More custom methods
}
```

Implement it in a cache class.

```csharp
public class JsonCache : IJsonCache
{
    ...
}
```

Register it in the service collection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<IJsonCache, JsonCache>();
}
```

Inject it into the repository.

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

Alternatively, use a factory class to provide a singleton [ICache](/interface/icache) instance.

```csharp
public static CacheFactory
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
                    _cache = new MemoryCache();
                }
            }
        }
        return _cache;
    }
}
```

Pass it to [Query](/operation/query) or [QueryAll](/operation/queryall) operations.

```csharp
// Factory class as pointer to the cacher
var memoryCache = CacheFactory.CreateCacher();

// Pass it during the calls
using (var connection = new SqlConnection(connectionString))
{
    var products = connection.QueryAll<Product>(cacheKey: "CacheKey:Products", cache: memoryCache);
}
```

Or pass it to the [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository) constructor.

```csharp
// Pass it on the constructor
public class NorthwithRepository : DbRepository<SqlConnection>
{
    public NorthwithRepository(IAppSettings settings)
        : base(settings.Value.ConnectionString, CacheFactory.CreateCacher())
    { }
}
```
