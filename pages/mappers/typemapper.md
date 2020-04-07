---
layout: navpage
sidebar: mappers
title: "TypeMapper (RepoDb)"
permalink: /mapper/typemapper
tags: [repodb, class, typemapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# TypeMapper

A static class that is used to map a .NET CLR Type into its equivalent database type.

#### Properties

Below are the properties available from this class.

- `ConversionType` - is used as a conversion type when converting the instance of `DbDataReader` object into its destination .NET CLR types. The default value is [ConversionType.Default](/enumeration/conversiontype).

#### Methods

Below are the methods available from this class.

- `Map` - adds a mapping between the .NET CLR Type and database type.
- `Get` - gets the mapped database type based on the .NET CLR Type.
- `Unmap` - removes the mapping between the .NET CLR Type and database type.

#### Use-Cases

You should use this class if you would like to override the [ConversionType](/enumeration/conversiontype) of the library and if you would like to override the ADO.NET default mapping between .NET CLR Type and database type.

#### How to Use?

To add a mapping, simply call the `Map` method.

```csharp
TypeMapper.Map(typeof(DateTime), DbType.DateTime2);
```

To get the mapping, use the `Get` method.

```csharp
var dbType = TypeMapper.Get(typeof(DateTime));
```

To remove the mapping, use the `Unmap` method.

```csharp
TypeMapper.Unmap(typeof(DateTime));
```
