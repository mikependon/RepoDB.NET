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

This enum is used to set the value of the `ConversionType` property of [Converter](/mapper/converter) object. It defines how the conversion type is when extracting the [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) into its equivalent .NET CLR type.

## Enum Values

Below is the list of enum values.

| Name | Description |
|:-----|:------------|
| Default | The conversion is strict. There is no additional implied logic in used during the conversion of the [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) object into its destination .NET CLR type. |
| Automatic | The conversion is not strict (or automatic). An additional logic from the `System.Linq.Expressions.Expression.Convert(Expression, Type)` and/or `System.Convert` objects will be used to properly map the instance of [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) object into its destination .NET CLR type. The operation is compiled ahead-of-time (AOT) and will only succeed if the data types of both objects are convertible. |

## Use-Cases

You can use the value of `Automatic` if the database column type is not the same as the class property type (i.e.: `Int` to [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0), [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0) to `Guid`, [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0) to [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0), etc).

Let us say, you have a given class named `Customer` as defined below.

```csharp
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

Then, the library will succeed if the table is defined like below.

```csharp
CREATE TABLE [dbo].[Customer]
{
    [Id] INT IDENTITY(1, 1) NOT NULL,
    [Name] NVARCHAR(32) NOT NULL,
    [Age] INT NOT NULL
}
```

However, the library will fail if the table is defined like below.

```csharp
CREATE TABLE [dbo].[Customer]
{
    [Id] INT IDENTITY(1, 1) NOT NULL,
    [Name] NVARCHAR(32) NOT NULL,
    [Age] NVARCHAR(8) NOT NULL -- There is no explicit converter between STRING and INT
}
```

## Usability

Simply set the value of `ConversionType` property of the [Converter](/class/converter) object like below.

```csharp
Converter.ConversionType = ConversionType.Automatic;
```

{: .important }
> Please be reminded that if the database column is not auto-convertible to a target property, then the `System.Convert` method will used during the transformation. With this additional logic implied, the performance will be affected during the transformations.