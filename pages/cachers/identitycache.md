---
layout: page
title: "IdentityCache (RepoDb)"
permalink: /cacher/identitycache
tags: [repodb, class, identitycache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## IdentityCache

A cacher class for the class identity property. It provides a 2nd-layer caching for the library when it comes to the class identity property extraction. As a result, the library is fast-enough when reusing the already extracted class identity property on any execution.

#### Methods

Below are the methods available from this class.

- `Flush` - allows you to flush the caches.
- `Get` - returns the list of [Field](/class/field) objects.

#### Use-Cases

You should only use this class if you would like to get the references of the class identity property in general purpose.

#### How to Use?

You can simply call the `Get()` method of this class by passing the class type.

```csharp
var identity = IdentityCache.Get(typeof(Person));
// Use the 'identity' here
```

Or, via generic type.

```csharp
var identity = IdentityCache.Get<Person>();
// Use the 'identity' here
```

> Internally, it uses the [PropertyCache](/cacher/propertycache) class to extract the list of [ClassProperty](/class/classproperty) and filter the result using the given [IsIdentity()](/class/classproperty#isidentity) method.