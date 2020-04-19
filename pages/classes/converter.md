---
layout: navpage
sidebar: classes
title: "Converter"
permalink: /class/converter
tags: [repodb, class, converter, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Converter

This class is used as the converter for the library. It also handles the value of the `ConversionType` which affects the conversion behavior of the library when reading or pushing the data in the database. 

#### Properties

Below are the properties available from this class.

- `ConversionType` - is used as a conversion type when converting the instance of `DbDataReader` object into its destination .NET CLR types. The default value is [ConversionType.Default](/enumeration/conversiontype).

#### Methods

- `DbNullToNull` - a method that is being used to check if the value is `DBNull.Value`. If yes, then it converts it to `null`.
- `ToType` - a method that is being used to check whether the type of the value is same with the defined target type, otherwise it converts it.

#### Setting to Automatic Conversion

To set the library to auto-convert the conversion of the .NET CLR types and the database types, simply set the value of `ConversionType` to `Automatic`.

```csharp
Converter.ConversionType = ConversionType.Automatic;
```

> Please see the [ConversionType](/enumeration/conversiontype) enumeration to learn more about this conversion.

#### DbNull Conversion

You can use the `DbNullToNull` method to auto-convert the `DbDataReader` result into a `null` object if it the value is `DBNull.Value`.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    using (var reader = connection.ExecuteReader())
    {
        var name = Converter.DbNullToNull(reader["Name"]);
    }
}
```

#### Converting to Specific Type

You can use the `ToType` method to convert the type into a specific type. It first check if the type is equal, then it simply returns it, otherwise it is using the `System.ChangeType` method and cast it back to the target type.

```csharp
var value = "12345";
var converted = Converter.ToType<int>(value);
```