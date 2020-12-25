---
layout: default
sidebar: mappers
title: "IdentityMapper"
description: "A mapper class that is used to map a class property to be an identity property. This class is used as an alternative to Identity attribute."
permalink: /mapper/identitymapper
tags: [repodb, class, identitymapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Mappers
---

# IdentityMapper

---

A mapper class that is used to map a class property to be an identity property. This class is used as an alternative to [Identity](/attribute/identity) attribute.

### Methods

Below are the methods available from this class.

- `Add` - adds an identity property mapping into an entity type.
- `Clear` - clears all the classes primary property mappings.
- `Get` - gets the instance of [ClassProperty](/class/classproperty) that is mapped as identity key.
- `Remove` - removes the exising mapped identity property of the data entity.

### Use-Cases

You should use this class if you do not like to use the [Identity](/attribute/identity) attribute. Usually, the purpose of the usability is to make sure that the model is attribute-free and is not bound to a specific ORM.

### How to use?

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