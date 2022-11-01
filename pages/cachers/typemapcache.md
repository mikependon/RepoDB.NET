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

A cacher class for the database type objects that has been mapped to .NET CLR type or class property. Underneath, it uses the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class to extract the results and caching it for future use.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the instance of the database type (via [DbType](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0) object). |

## Usability

You can simply call the `Get()` method of this class by passing the class type.

#### Type Level

```csharp
var dbType = TypeMapCache.Get(typeof(int));
// Use the 'dbType' here
```

Or via generic.

```csharp
var dbType = TypeMapCache.Get<int>();
// Use the 'dbType' here
```

#### Property Level

```csharp
var propertyHandler = TypeMapCache.Get<Customer>("Address");
// Use the 'propertyHandler' here
```

Or via generic.

```csharp
var dbType = TypeMapCache.Get<Customer>(e => e.Address);
// Use the 'dbType' here
```