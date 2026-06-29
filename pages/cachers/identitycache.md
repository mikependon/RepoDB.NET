---
layout: default
sidebar: cachers
title: "IdentityCache"
description: "A class that is being used to retrieve the cached identity field of the class or data entity."
permalink: /cacher/identitycache
tags: [repodb, identitycache]
parent: CACHERS
---

# IdentityCache

---

A cacher class for the class identity property. It uses [IdentityMapper](/mapper/identitymapper) internally to extract results and caches them for future use.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the list of [Field](/class/field) objects. |

## Usability

Call the `Get()` method by passing the class type:

```csharp
var identity = IdentityCache.Get(typeof(Person));
// Use the 'identity' here
```

Or via generic type:

```csharp
var identity = IdentityCache.Get<Person>();
// Use the 'identity' here
```

{: .note }
> Internally, it uses [IdentityMapper](/mapper/identitymapper) and [PropertyCache](/cacher/propertycache) to extract [ClassProperty](/class/classproperty) objects and filter them using [IsIdentity()](/class/classproperty#isidentity).