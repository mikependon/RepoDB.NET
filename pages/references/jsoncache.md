---
layout: default
sidebar: references
title: "JSON Cache"
nav_order: 5
permalink: /reference/jsoncache
tags: [repodb, class, jsoncache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: REFERENCES
---

# JSON Cache

---

This page contains the reference implementation when implementing a file system cache object that is based for JSON. The consolidated output of this page can be found [here](/reference/output/jsoncache).

> The class is using the [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) package.

### Implementation

First, create an interface that implements an [ICache](/interface/icache) interface. This is also to support the dependency injection.

```csharp
public interface IJsonCache : ICache
{
    // Properties
    string Extension { get; }
    string Path { get; }

    // Methods
    string GetFileName(string key);
    void EnsureDirectory();
}
```

Then, create a class that implements your newly created interface.

```csharp
public class JsonCache : IJsonCache
{
    public JsonCache(string path,
        string extension)
    {
        ...
    }
}
```

> All methods of the [ICache](/interface/icache) interface must be implemented manually.

### Properties

Implement the properties that will hold the extension of the file and the location of the caches.

```csharp
public string Extension { get; }
public string Path { get; }
```

### Methods

Implement the helper methods that would help you compose the filename and creates a directory.

```csharp
public string GetFileName(string key)
{
    var fileName = $"{Regex.Replace(key, "[^a-zA-Z0-9 -]", "_")}.{Extension}";
    return System.IO.Path.Combine(Path, fileName);
}

public void EnsureDirectory()
{
    if (Directory.Exists(Path) == false)
    {
        Directory.CreateDirectory(Path);
    }
}
```

Then, implement each method of the [ICache](/interface/icache) interface itself.

#### Add

```csharp
public void Add<T>(string key,
    T value,
    int expiration = 180,
    bool throwException = true)
{
    var fileName = GetFileName(key);
    var contains = Contains(key);
    if (contains)
    {
        if (throwException)
        {
            throw new Exception($"File '{fileName}' already exists.");
        }
    }
    File.WriteAllText(fileName, JsonConvert.SerializeObject(value));
}

public void Add<T>(CacheItem<T> item,
    bool throwException = true)
{
    Add<T>(item.Key, item.Value, item.CacheItemExpiration, throwException);
}
```

#### Clear

```csharp
public void Clear()
{
    Directory.Delete(Path, true);
    EnsureDirectory();
}
```

#### Contains

```csharp
public bool Contains(string key)
{
    var fileName = GetFileName(key);
    return File.Exists(fileName);
}
```

#### Get

```csharp
public CacheItem<T> Get<T>(string key,
    bool throwException = true)
{
    var fileName = GetFileName(key);
    if (File.Exists(fileName))
    {
        var value = JsonConvert.DeserializeObject<T>(File.ReadAllText(fileName));
        return new CacheItem<T>(key, value);
    }
    if (throwException)
    {
        throw new FileNotFoundException($"File '{fileName}' is not found.");
    }
    return null;
}
```

#### Remove

```csharp
public void Remove(string key,
    bool throwException = true)
{
    var fileName = GetFileName(key);
    if (File.Exists(fileName))
    {
        File.Delete(fileName);
    }
    if (throwException)
    {
        throw new FileNotFoundException($"File '{fileName}' is not found.");
    }
}
```

#### GetEnumerator

```csharp
IEnumerator IEnumerable.GetEnumerator()
{
    foreach (var fileName in Directory.GetFiles(Path))
    {
        yield return JsonConvert.DeserializeObject<object>(File.ReadAllText(fileName));
    }
}
```

> The class [CacheItem](/class/cacheitem) does not have default constructor, therefore, you cannot serialize/deserialize this object into JSON. The only thing that you can serialize/deserialize is the value itself. If you wish to cache the properties (i.e.: `Key`, `Expiration`, `ExpirationInMinutes`), then you have to create a class in between before serializing/deserializing it.

### Usability

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
                    _cache = new JsonCache();
                }
            }
        }
        return _cache;
    }
}
```

Or inject it as a singleton object.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<IJsonCache, JsonCache>();
}
```