---
layout: default
sidebar: enumerations
title: "ConversionType"
description: "A enumeration that defines the type of conversion to be used when extracting a data from/to the database."
permalink: /enumeration/conversiontype
tags: [repodb, conversiontype]
parent: ENUMERATIONS
---

# ConversionType

---

This enum sets the `ConversionType` property of the [Converter](/class/converter) object. It controls how a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) is converted into its equivalent .NET CLR type.

## Enum Values

| Name | Description |
|:-----|:------------|
| Default | The conversion is strict. No additional implied logic is applied during the conversion of the [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) object into its destination .NET CLR type. |
| Automatic | The conversion is non-strict (automatic). Additional logic from `System.Linq.Expressions.Expression.Convert(Expression, Type)` and/or `System.Convert` is applied to map the [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) object into its destination .NET CLR type. The operation is compiled ahead-of-time (AOT) and will only succeed if both data types are convertible. |

## Use-Cases

Use `Automatic` when the database column type differs from the class property type (e.g., `Int` to [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0), [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0) to `Guid`, [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0) to [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0), etc).

Given the following class:

```csharp
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

The library will succeed when the table is defined as:

```csharp
CREATE TABLE [dbo].[Customer]
{
    [Id] INT IDENTITY(1, 1) NOT NULL,
    [Name] NVARCHAR(32) NOT NULL,
    [Age] INT NOT NULL
}
```

However, the library will fail when the table is defined as:

```csharp
CREATE TABLE [dbo].[Customer]
{
    [Id] INT IDENTITY(1, 1) NOT NULL,
    [Name] NVARCHAR(32) NOT NULL,
    [Age] NVARCHAR(8) NOT NULL -- There is no explicit converter between STRING and INT
}
```

## Usability

Set the `ConversionType` property on the [Converter](/class/converter) object.

```csharp
Converter.ConversionType = ConversionType.Automatic;
```

{: .important }
> Please be reminded that if the database column is not auto-convertible to a target property, then the `System.Convert` method will used during the transformation. With this additional logic implied, the performance will be affected during the transformations.
