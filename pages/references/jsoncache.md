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

public void Add<T>(CacheItem<T> item,
    bool throwException = true)
{
    var fileName = GetFileName(item.Key);
    var contains = Contains(item.Key);
    if (contains)
    {
        if (throwException)
        {
            throw new Exception($"File '{fileName}' already exists.");
        }
    }
    File.WriteAllText(fileName, JsonConvert.SerializeObject(item));
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
        return JsonConvert.DeserializeObject<CacheItem<T>>(File.ReadAllText(fileName));
    }
    if (throwException)
    {
        throw new FileNotFoundException($"File '{fileName}' is not found.");
    }
    return null;
}

public IEnumerator<CacheItem<T>> GetEnumerator()
{
    foreach (var fileName in Directory.GetFiles(Path))
    {
        yield return JsonConvert.DeserializeObject<CacheItem<T>>(File.ReadAllText(fileName));
    }
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
    return GetEnumerator();
}
```