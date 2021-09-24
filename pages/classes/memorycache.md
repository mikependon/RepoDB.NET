---
layout: default
sidebar: classes
title: "MemoryCache"
description: "An advance class that is used to cache an object into a memory within the library."
permalink: /class/memorycache
tags: [repodb, class, memorycache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: CLASSES
---

# MemoryCache

---

This class gives your application the maximum performance as it eliminates the round-trips towards the database for the next 180 minutes (overridable). It implements the [ICache](/interface/icache) interface.

> This class is used as the default cacher-class of the library.

### Use-Cases

You should use this class if you wish to cache all the fetched objects from the database into memory.

### Usability

Simply pass the instance in the constructor of the repositories (i.e.: [BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository)) or when calling the fetched operations (i.e.: [Query](/operation/query) and [QueryAll](/operation/queryall)).

### Customize

Create a custom interface that implements the [ICache](/interface/icache) interface.

```csharp
public interface IJsonCache : ICache
{
    // More custom methods
}
```

Then, implement it in the cache class.

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
        : base(settings.Value.ConnectionString, cache)
    { }

    ...
}
```

Alternatviely, you can create a factory class that returns an instance of [ICache](/interface/icache) object.

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

Then, pass it when calling the [Query](/operation/query) and [QueryAll](/operation/queryall) operations.

```csharp
// Factory class as pointer to the cacher
var memoryCache = CacheFactory.CreateCacher();

// Pass it during the calls
using (var connection = new SqlConnection(connectionString))
{
    var products = connection.QueryAll<Product>(cacheKey: "CacheKey:Products", cache: memoryCache);
}
```

Or pass it in the constructor of the [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository) objects.

```csharp
// Pass it on the constructor
public class NorthwithRepository : DbRepository<SqlConnection>
{
    public NorthwithRepository(IAppSettings settings)
        : base(settings.Value.ConnectionString, CacheFactory.CreateCacher())
    { }
}
```

