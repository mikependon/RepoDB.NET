---
layout: page
title: "CacheItem (RepoDb)"
permalink: /class/cacheitem
tags: [repodb, class, cacheitem, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## CacheItem

This class is used as an item for the [ICache](/interface/icache) object.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Methods

Please see the [IExpirable](/interface/iexpirable) methods.

#### Properties

- `Key` - the key to the cache.
- `Value` - the value of the class (a generic type).
- `CacheItemExpiration` - the expiration of the cache in minutes.

In addition, please see the [IExpirable](/interface/iexpirable) properties.

#### Properties

Below are the methods available from this interface.


##### Creating New Instance

Below is the way on how to create a new instance of this class.

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
var item = new CacheItem<IEnumerable<Person>>("CacheKey:ActivePeople", people, expirationInMinutes);

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
var item = repository.Cache.Get<IEnumerable<Person>>("CacheKey:ActivePeople");
```

> If the cache is not found on the given key, by default it will throw an exception. You can set the `throwException` argument to `false` if you wish not to throw an exception.

