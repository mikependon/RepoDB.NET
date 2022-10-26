---
layout: default
sidebar: mappers
title: "IdentityMapper"
description: "A mapper class that is being used to map a class property to be an identity property. This class is used as an alternative to Identity attribute."
permalink: /mapper/identitymapper
tags: [repodb, identitymapper]
parent: MAPPERS
---

# IdentityMapper

---

A mapper class that is being used to map a class property to be an identity property. This class is used as an alternative to the [Identity](/attribute/identity) attribute.

### Methods

Below are the methods available from this class.

- `Add` - adds an identity property mapping into a class.
- `Clear` - clears all the exising identity property mappings.
- `Get` - gets the existing mapped identity property of the class.
- `Remove` - removes the exising mapped identity property of the class.

### Usability

To add a mapping, simply call the `Add()` method.

```csharp
IdentityMapper.Add<Customer>(c => c.Id, true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get()` method.

```csharp
var property = IdentityMapper.Get<Customer>();
```

> Please consider to always use the [IdentityCache](/cacher/identitycache) class when extracting the mapped class property identity.

To remove the mapping, use the `Remove()` method.

```csharp
IdentityMapper.Remove<Customer>();
```