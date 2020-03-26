---
layout: page
title: "JSON Cache (RepoDb)"
permalink: /reference/output/jsoncache
tags: [repodb, class, jsoncache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

#### JsonCache (File System)

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

    /*** Helpers ***/

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

    /*** Methods ***/

    public void Add(string key,
        object value,
        int expiration = 180,
        bool throwException = true)
    {
        Add(new CacheItem(key, value, expiration), throwException);
    }

    public void Add(CacheItem item,
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
        var contents = JsonConvert.SerializeObject(item.Value);
        File.WriteAllText(fileName, contents);
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

    public CacheItem Get<T>(string key,
        bool throwException = true)
    {
        var fileName = GetFileName(key);
        if (File.Exists(fileName))
        {
            var value = JsonConvert.DeserializeObject<IEnumerable<Person>>(File.ReadAllText(fileName));
            return new CacheItem(key, value);
        }
        if (throwException)
        {
            throw new FileNotFoundException($"File '{fileName}' is not found.");
        }
        return null;
    }

    public IEnumerator<CacheItem> GetEnumerator()
    {
        foreach (var fileName in Directory.GetFiles(Path))
        {
            yield return JsonConvert.DeserializeObject<CacheItem>(File.ReadAllText(fileName));
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
}
```