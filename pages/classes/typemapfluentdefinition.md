---
layout: default
sidebar: classes
title: "TypeMapFluentDefinition"
description: "A class that is being used to define a type-level mappings (i.e.: DB Type and Property Handler)."
permalink: /class/typemapfluentdefinition
tags: [repodb, typemapfluentdefinition]
parent: CLASSES
---

# TypeMapFluentDefinition

---

A class that is being used to define a type level mappings for the database types and property handlers. It is the result of the `Type()` method of the [FluentMapper](/mapper/fluentmapper) mapper class.

{: .important }
> The mapping will be applied (at the least) to all data entity properties with the same .NET CLR type. So please be reminded of this.

## Mapping a Database Type

To map an equivalent database type into a .NET CLR type, use the [DbType](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0) method.

```csharp
var definition = FluentMapper.Type<DateTime>();
definition.DbType(DbType.DateTime2);
```

It is using the [TypeMapper](/mapper/typemapper) class underneath.

## Defining a Property Handler

To define the property handler for the data entity property, use the `PropertyHandler` method.

```csharp
var definition = FluentMapper.Entity<DateTime>();
definition.PropertyHandler<DateTimeToUtcKindTypeHandler>();
```

It is using the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class underneath.