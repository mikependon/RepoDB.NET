---
layout: navpage
sidebar: mappers
title: "TypeMapper"
description: "A class that is used to map a .NET CLR type or class property into its equivalent database type. This class is used as an alternative to TypeMap attribute."
permalink: /mapper/typemapper
tags: [repodb, class, typemapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# TypeMapper

A class that is used to map a .NET CLR type or class property into its equivalent database type (via `DbType` object). This class is used as an alternative to [TypeMap](/attribute/typemap) attribute.

#### Methods

Below are the methods available from this class.

- `Add` - adds a mapping between the .NET CLR type and database type.
- `Clear` - clears all the database type mappings.
- `Get` - gets the mapped database type based on the .NET CLR type.
- `Remove` - removes the mapping between the .NET CLR type and database type.

#### Use-Cases

You should use this class if you do not like to use the [TypeMap](/attribute/typemap) attribute. Usually, the purpose of the usability is to make sure that the model is attribute-free and is not bound to a specific ORM.

#### How to use?

Let us say you would like to map the `System.DateTime` .NET CLR type into a `DbType.DateTime2` database type.

###### Property Level Mapping

To add a property level mapping, simply call the `Add` method and pass the target property and the value of `DbType` object.

```csharp
TypeMapper.Add<Customer>(e => e.DateOfBirth, DbType.DateTime2, true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get` method.

```csharp
var dbType = TypeMapper.Get<Customer>(e => e.DateOfBirth);
```

To remove the mapping, use the `Remove` method.

```csharp
TypeMapper.Remove<Customer>(e => e.DateOfBirth);
```

###### Type Level Mapping

To add a class level mapping, simply call the type-level `Add` method and pass the target .NET CLR type and the value of `DbType` object.

```csharp
TypeMapper.Add<DateTime>(DbType.DateTime2, true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the type-level `Get` method.

```csharp
var propertyHandler = TypeMapper.Get<DateTime>();
```

To remove the mapping, use the type-level `Remove` method.

```csharp
TypeMapper.Remove<DateTime>();
```