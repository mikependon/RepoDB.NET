---
layout: navpage
sidebar: enumerations
title: "ConversionType"
description: "A enumeration that defines the type of conversion to be used when extracting a data from/to the database."
permalink: /enumeration/conversiontype
tags: [repodb, class, conversiontype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# ConversionType

This enum is used to set the value of the `ConversionType` property of [TypeMapper](/mapper/typemapper) object. It defines how the conversion type is when extracting the `DbDataReader` into its equivalent .NET CLR type.

#### Enum Values

- `Default` - the conversion is strict and there is no additional implied logic during the conversion of the `DbDataReader` object into its destination .NET CLR type.
- `Automatic` - the data type conversion is not strict. An additional logic from `System.Convert` object will be used to properly map the `DbDataReader` data type into its destination .NET CLR type. The operation will only succeed if the data types are convertible.

#### Use-Cases

You can use the value of `Automatic` if you have a database column where the type is not convertible to a property of class (i.e.: `Int` to `String`, `String` to `Guid`, `DateTime` to `String`, etc).

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

#### How to Use?

Simply set the value of `ConversionType` property of [TypeMapper](/mapper/typemapper) object like below.

```csharp
TypeMapper.ConversionType = ConversionType.Automatic;
```

> Please be reminded that if the database column is not auto-convertible to a target property, then the `System.Convert` method will be explicitly be used during the transformation. With this additional logic implied, the performance will be affected during transformation.