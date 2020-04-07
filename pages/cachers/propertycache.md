---
layout: navpage
sidebar: cachers
title: "PropertyCache (RepoDb)"
permalink: /cacher/propertycache
tags: [repodb, class, propertycache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# PropertyCache

A hugely-used cacher class for the [ClassProperty](/class/classproperty). It provides a 2nd-layer caching for the library when it comes to the class properties extraction. As a result, the library is fast-enough when reusing the already extracted class properties on any execution.

> Internally, this class is widely used within the library.

#### Methods

Below are the methods available from this class.

- `Flush` - allows you to flush the caches.
- `Get` - returns the list of [ClassProperty](/class/classproperty) objects.

#### Use-Cases

You should use this class if you would like to get the references of the class properties in general purpose.

#### How to Use?

You can simply call the `Get()` method of this class by passing the class type.

```csharp
var properties = PropertyCache.Get(typeof(Person));
// Use the 'properties' here
```

Or, via generic type.

```csharp
var properties = PropertyCache.Get<Person>();
// Use the 'properties' here
```

> Internally, it uses the [ClassExpression](/class/classexpression) class to extract and cache the list of [ClassProperty](/class/classproperty) objects.