---
layout: default
sidebar: classes
title: "CacheItem"
description: "A class that is used as an item for the cache object."
permalink: /class/cacheitem
tags: [repodb, cacheitem]
parent: CLASSES
---

# CacheItem

---

This class represents an item in the [ICache](/interface/icache) object. It implements the [IExpirable](/interface/iexpirable) interface.

## Properties

| Name | Description |
|:-----|:------------|
| Key | The cache key. |
| Value | The cached value (generic type). |
| CacheItemExpiration | The cache expiration in minutes. |

## Creating New Instance

```csharp
var expirationInMinutes = 180;
var item = new CacheItem("Key", "Value", expirationInMinutes);
```

## Passing to the Cache

The [ICache](/interface/icache) object is typically embedded in a repository. The following example manually adds an item to the cache of a `PersonRepository`.

```csharp
var people = GetPeople();
var expirationInMinutes = 180;
var item = new CacheItem<IEnumerable<Person>>("CacheKey:ActivePeople", people, expirationInMinutes);

using (var repository = new PersonRepository())
{
    repository.Cache.Add(item);
}
```

{: .note }
> The cache is set automatically when a `cacheKey` is passed to the [Query](/operation/query) or [BatchQuery](/operation/batchquery) operations.

## Retrieving from the Cache

Access the [ICache](/interface/icache) object directly and call `Get()` with the key.

```csharp
var item = repository.Cache.Get<IEnumerable<Person>>("CacheKey:ActivePeople");
```

{: .note }
> If no cache entry exists for the given key, an exception is thrown by default. Set the `throwException` argument to `false` to suppress this.
