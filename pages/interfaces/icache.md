---
layout: default
sidebar: interfaces
title: "ICache"
permalink: /interface/icache
tags: [repodb, class, icache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: INTERFACES
---

# ICache

---

This interface is used to mark a class to be a cache object. It gives you the full control on the implementations of your own cache object (i.e.: File Caching, SqLite Caching, XML Caching, Memory Caching, etc).

### Methods

Below are the methods available from this interface.

- `Add` - allows you to add a new cache item value into the cache storage.
- `Clear` - allows you to clear the cache storage.
- `Contains` - allows you to check whether the cache item exists by cache-key.
- `Get` - allows you to get an instance of the cache by cache-key.
- `Remove` - allows you to remove an existing cache item from the cache storage.

### Use-Cases

You can use this feature to maximize the performance by using the 2nd layer cache for the static and look-up records (or those records that are not frequently changing or not changing at all).

### How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class JsonCache : ICache
{
    public JsonCache(string path,
        string extension)
    {
        Path = path;
        Extension = extension;
        EnsureDirectory();
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

    public void Clear()
    {
        ...
    }

    ...
}
```

> You have to implement all the methods needed by this interface. With this, you have the full control of you cacher object.

### How to use?

You can pass it on the constructor of the [BaseRepository](/class/baserepository) object.

```csharp
public class StateRepository : BaseRepository<State, SqlConnection>
{
    public StateRepository(ISettings settings)
        : base(settings.Value.ConnectionString, new JsonCache(@"C:\cache", "jsoncache"))
    { }
}
```

And call the [query](/operation/query) operation like below.

```csharp
using (var repository = new StateRepository(new AppSettings()))
{
    var states = repository.QueryAll(cacheKey: "CacheKey:States");
}
```

Or even to the constructor of [DbRepository](/class/dbrepository) object.

```csharp
public class DatabaseRepository : DbRepository<SqlConnection>
{
    public DatabaseRepository(ISettings settings)
        : base(settings.Value.ConnectionString, new JsonCache(@"C:\cache", "jsoncache"))
    { }
}
```

And call the [query](/operation/query) operation like below.

```csharp
using (var repository = new DatabaseRepository(new AppSettings()))
{
    var states = repository.QueryAll<State>(cacheKey: "CacheKey:States");
}
```

> Please consider to only create a single cache object within the application and have it passed as a singleton object in any fetch-calls or reporitories.
