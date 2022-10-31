---
layout: default
sidebar: mappers
title: "TypeMapper"
description: "A class that is being used to map a .NET CLR type or class property into its equivalent database type. This class is used as an alternative to TypeMap attribute."
permalink: /mapper/typemapper
tags: [repodb, typemapper]
parent: MAPPERS
---

# TypeMapper

---

A class that is being used to map a .NET CLR type or class property into its equivalent database type (via `DbType` object). This class is used as an alternative to the [TypeMap](/attribute/typemap) attribute.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Add | Adds a mapping between a .NET CLR type and a database type. |
| Clear | Clears all the existing database type mappings. |
| Get | Gets the existing mapped database type on the .NET CLR type. |
| Remove | Removes the existing mapped database type on the .NET CLR type. |

## Usability

Let us say you would like to map the `System.DateTime` .NET CLR type into a `DbType.DateTime2` database type.

### Property Level Mapping

To add a property level mapping, simply call the `Add()` method and pass the target property and the value of `DbType` object.

```csharp
TypeMapper.Add<Customer>(e => e.DateOfBirth, DbType.DateTime2, true);
```

{: .warning }
> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get()` method.

```csharp
var dbType = TypeMapper.Get<Customer>(e => e.DateOfBirth);
```

To remove the mapping, use the `Remove()` method.

```csharp
TypeMapper.Remove<Customer>(e => e.DateOfBirth);
```

### Type Level Mapping

To add a class level mapping, simply call the type-level `Add()` method and pass the target .NET CLR type and the value of `DbType` object.

```csharp
TypeMapper.Add<DateTime>(DbType.DateTime2, true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the type-level `Get()` method.

```csharp
var propertyHandler = TypeMapper.Get<DateTime>();
```

To remove the mapping, use the type-level `Remove()` method.

```csharp
TypeMapper.Remove<DateTime>();
```