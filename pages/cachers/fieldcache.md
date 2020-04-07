---
layout: navpage
sidebar: cachers
title: "FieldCache (RepoDb)"
permalink: /cacher/fieldcache
tags: [repodb, class, fieldcache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# FieldCache

A cacher class for the class properties (as fields). It provides a 2nd-layer caching for the library when it comes to the class properties extraction (as fields). As a result, the library is fast-enough when reusing the already extracted class properties (as fields) on any execution.

> Internally, this class is widely used within the library.

#### Methods

Below are the methods available from this class.

- `Flush` - allows you to flush the caches.
- `Get` - returns the list of [Field](/class/field) objects.

#### Use-Cases

You should use this class if you would like to get the references of the class properties (as fields) specially when composing a SQL statement or would like to lists it.

#### How to Use?

You can simply call the `Get()` method of this class by passing the class type.

```csharp
var fields = FieldCache.Get(typeof(Person));
// Use the 'fields' here
```

Or, via generic type.

```csharp
var fields = FieldCache.Get<Person>();
// Use the 'fields' here
```

> Internally, it uses the [PropertyCache](/cacher/propertycache) class to extract the list of [ClassProperty](/class/classproperty) and convert and cache it all as an `Enumerable<Field>`.