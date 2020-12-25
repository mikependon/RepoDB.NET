---
layout: default
sidebar: cachers
title: "PropertyCache"
description: "A class that is being used to retrieve the cached properties of the class or data entity."
permalink: /cacher/propertycache
tags: [repodb, class, propertycache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Cachers
---

# PropertyCache

---

A hugely-used cacher class for the [ClassProperty](/class/classproperty). It provides a 2nd-layer caching for the library when it comes to the class properties extraction. As a result, the library is fast-enough when reusing the already extracted class properties on any execution.

> Internally, this class is widely used within the library.

### Methods

Below are the methods available from this class.

- `Flush` - allows you to flush the caches.
- `Get` - returns the list of [ClassProperty](/class/classproperty) objects.

### Use-Cases

You should use this class if you would like to get the references of the class properties in general purpose.

### How to use?

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

> Internally, it uses the [PropertyMapper](/mapper/propertymapper) and [ClassExpression](/class/classexpression) classes to extract and cache the list of [ClassProperty](/class/classproperty) objects.