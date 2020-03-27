---
layout: page
title: "JSON Cache (RepoDb)"
permalink: /reference/jsoncache
tags: [repodb, class, jsoncache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## JSON Cache

This page contains the recommended way of implementing a cacher class for JSON objects in the file system.

The consolidated output of this page can be found [here](/reference/output/jsoncache).

#### Class Creation

Create a class that inherits the [ICache](/interface/icache) interface.

```csharp
public class JsonCache : ICache
{
    public JsonCache(string path,
        string extension)
    {
        ...
    }
}
```

> All methods must be implemented manually.

#### Properties

The properties that holds the extension of the file and the location of the cache.

```csharp
public string Extension { get; }
public string Path { get; }
```

#### Helper Methods

A method that compose the filename and creates directory.

```csharp
private string GetFileName(string key)
{
    var fileName = $"{Regex.Replace(key, "[^a-zA-Z0-9 -]", "_")}.{Extension}";
    return System.IO.Path.Combine(Path, fileName);
}

private void EnsureDirectory()
{
    if (Directory.Exists(Path) == false)
    {
        Directory.CreateDirectory(Path);
    }
}
```

#### Cache Methods

Implement each method of the [ICache](/interface/icache) interface.

```csharp
/*** Methods ***/

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

public void Clear()
{
    Directory.Delete(Path, true);
    EnsureDirectory();
}

public bool Contains(string key)
{
    var fileName = GetFileName(key);
    return File.Exists(fileName);
}

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

IEnumerator IEnumerable.GetEnumerator()
{
    foreach (var fileName in Directory.GetFiles(Path))
    {
        yield return JsonConvert.DeserializeObject<object>(File.ReadAllText(fileName));
    }
}
```

> The class [CacheItem](/class/cacheitem) does not have default constructor, by then, you can serialize/deserialize this object into JSON. The only items you can serialize/deserialize is the value itself. If you wish to cache the properties (ie: `Key`, `Expiration`, `ExpirationInMinutes`), then you have to create a class in between before serializing/deserializing it.