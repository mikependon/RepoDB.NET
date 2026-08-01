---
layout: default
sidebar: cachers
title: "TypeMapCache"
description: "A class that is being used to retrieve the cached mapped database type of the class or data entity property."
permalink: /cacher/typemapcache
tags: [repodb, typemapcache]
parent: CACHERS
---

# TypeMapCache

---

A cacher class for database type objects mapped to .NET CLR types or class properties. It uses [PropertyHandlerMapper](/mapper/propertyhandlermapper) internally to extract results and caches them for future use.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the instance of the database type (via [DbType](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0) object). |

## Usability

Call the `Get()` method by passing the class type.

#### Type Level

```csharp
var dbType = TypeMapCache.Get(typeof(int));
// Use the 'dbType' here
```

Or via generic type:

```csharp
var dbType = TypeMapCache.Get<int>();
// Use the 'dbType' here
```

#### Property Level

```csharp
var propertyHandler = TypeMapCache.Get<Customer>("Address");
// Use the 'propertyHandler' here
```

Or via expression:

```csharp
var dbType = TypeMapCache.Get<Customer>(e => e.Address);
// Use the 'dbType' here
```