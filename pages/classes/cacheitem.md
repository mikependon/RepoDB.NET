---
layout: page
title: "CacheItem (RepoDb)"
permalink: /class/cacheitem
tags: [repodb, class, cacheitem, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## CacheItem

A class that is used to define an item for a cache. It is usually used when defining a [ICache](/interface/icache) object.

> In this tutorial, we will use the [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer) as the database and [C#](https://docs.microsoft.com/en-us/dotnet/csharp/) as the programming language.

##### Creating New Instance

```csharp
var expirationInMinutes = 180;
var item = new CacheItem("Key", "Value", expirationInMinutes);
```

##### Passing to the Cache

Usually, the [ICache](/interface/icache) object is embedded within the repository. Let us say you have a repository named `PersonRepository`.

The code below is an explicit call to add a cache in a manual manner.

```csharp
var people = GetPeople();
var expirationInMinutes = 180;
var item = new CacheItem("CacheKey:ActivePeople", people, expirationInMinutes);

using (var repository = new PersonRepository())
{
    repository.Cache.Add(item);
}
```

> Usually, the cache will automatically be set by passing a value to the `cacheKey` argument when calling the [Query](/operation/query) or [BatchQuery](/operation/batchquery) operations.

##### Retrieving from the Cache

To retrieve an item from the cache, directly access the [ICache](/interface/icache) object and call the `Get` method passing the key.

Let us say, the repository `PersonRepository` is existing.

```csharp
var item = repository.Cache.Get("CacheKey:ActivePeople");
```

> If the cache is not found on the given key, by default it will thrown an exception. You can set the `throwException` argument to `false` to let it not throw an exception.

