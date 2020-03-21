---
layout: page
title: "PrimaryCache (RepoDb)"
permalink: /cacher/primarycache
tags: [repodb, class, primarycache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## PrimaryCache

A cacher class for the class primary property. It provides a 2nd-layer caching for the library when it comes to the class primary property extraction. As a result, the library is fast-enough when reusing the already extracted class primary property on any execution.

> Internally, this class is widely used within the library.

#### Methods

Below are the methods available from this class.

- Flush - allows you to flush the caches.
- Get - returns the list of [Field](/class/field) objects.

#### Use-Cases

You only use this class if you would like to get the references of the class primary property in general purpose.

#### How to Use?

You can simply call the `Get()` method of this class by passing the class type.

```csharp
var primary = PrimaryCache.Get(typeof(Person));
// Use the 'primary' here
```

Or, via generic type.

```csharp
var primary = PrimaryCache.Get<Person>();
// Use the 'primary' here
```

> Internally, it uses the [PropertyCache](/cacher/propertycache) class to extract the list of [ClassProperty](/class/classproperty) and filter the result using the given [IsPrimary()](/class/classproperty#isprimary) method.