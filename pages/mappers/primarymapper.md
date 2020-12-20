---
layout: navpage
sidebar: mappers
title: "PrimaryMapper"
description: "A mapper class that is used to map a class property to be primary property. This class is used as an alternative to Primary attribute."
permalink: /mapper/primarymapper
tags: [repodb, class, primarymapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# PrimaryMapper

A mapper class that is used to map a class property to be primary property. This class is used as an alternative to [Primary](/attribute/primary) attribute.

#### Methods

Below are the methods available from this class.

- `Add` - adds a primary property mapping into an entity type.
- `Clear` - clears all the classes identity property mappings.
- `Get` - gets the instance of [ClassProperty](/class/classproperty) that is mapped as primary key.
- `Remove` - removes the exising mapped primary property of the data entity.

#### Use-Cases

You should use this class if you do not like to use the [Primary](/attribute/primary) attribute. Usually, the purpose of the usability is to make sure that the model is attribute-free and is not bound to a specific ORM.

#### How to use?

To add a mapping, simply call the `Add` method.

```csharp
PrimaryMapper.Add<Customer>(c => c.Id, true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get` method.

```csharp
var property = PrimaryMapper.Get<Customer>();
```

> Please consider to always use the [PrimaryCache](/cacher/identitycache) class when extracting the mapped class property primary.

To remove the mapping, use the `Remove` method.

```csharp
PrimaryMapper.Remove<Customer>();
```
