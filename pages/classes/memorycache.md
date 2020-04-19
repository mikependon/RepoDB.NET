---
layout: navpage
sidebar: classes
title: "MemoryCache (RepoDb)"
permalink: /class/memorycache
tags: [repodb, class, memorycache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# MemoryCache

This class gives your application the maximum performance as it eliminates the round-trips towards the database for the next 180 minutes (overridable). It implements the [ICache](/interface/icache) interface.

> This class is used as the default cacher-class of the library.

#### Use-Cases

You should use this class if you wish to cache all the fetched objects from the database into memory.

#### How to Use?

Simply pass the instance in the constructor of the repositories (ie: [BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository)) or when calling the fetched operations (ie: [Query](/operation/query) and [QueryAll](/operation/queryall)).

As a recommendation, create a factory class that returns the cacher.

```csharp
public static CacheFactory
{
    private static object m_syncLock = new object();
    private static ICache m_cache = null;
    
    public static ICache CreateCacher()
    {
        if (m_cache == null)
        {
            lock (m_syncLock)
            {
                if (m_cache == null)
                {
                    m_cache = new MemoryCache();
                }
            }
        }
        return m_cache;
    }
}
```

Then pass it when calling the [Query](/operation/query) and [QueryAll](/operation/queryall) operations.

```csharp
// Factory class as pointer to the cacher
var memoryCache = CacheFactory.CreateCacher();

// Pass it during the calls
using (var connection = new SqlConnection(connectionString).EnsureOpen())
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
        : base(settings.ConnectionString, CacheFactory.CreateCacher())
    { }
}
```

