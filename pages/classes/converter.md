---
layout: default
sidebar: classes
title: "Converter"
description: "A converter class that is being used to convert an object into a specific type within the library."
permalink: /class/converter
tags: [repodb, converter]
parent: CLASSES
---

# Converter

---

This class is used as the main converter class for the library. It also handle the mutable properties that could affect the conversion behavior of the library when reading or pushing the data towards the database. 

## Properties

Below is the list of properties.

| Name | Description |
|:-----|:------------|
| ConversionType | Is used as a conversion type when converting an instance of `DbDataReader` object into its destination .NET CLR types. The default value is [ConversionType.Default](/enumeration/conversiontype). |
| EnumDefaultDatabaseType | Is used as a default equivalent database type of an enumeration if it is being used as a parameter to the execution of any non-entity-based operations (i.e.: [ExecuteScalar](/operation/executescalar), [ExecuteNonQuery](/operation/executenonquery) and [ExecuteReader](/operation/executereader)). |

Deprecated
{: .label .label-red }

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| DbNullToNull | A method that is being used to check if the value is `DBNull.Value`. If yes, then it converts it to `null`. |
| ToType | A method that is being used to check whether the type of the value is same with the defined target type, otherwise it converts it. |

## Setting to Automatic Conversion

To set the library to auto-convert the conversion of the .NET CLR types and the database types, simply set the value of `ConversionType` to `Automatic`.

```csharp
Converter.ConversionType = ConversionType.Automatic;
```

Deprecated
{: .label .label-red }

{: .note }
> Please see the [ConversionType](/enumeration/conversiontype) enumeration to learn more about this conversion.

## When to use the Enum Default Database Type?

You should only use the enumeration default database type setting if you wish to override the default conversion of the library pertaining to enumeration. By default, the library is auto-converting the enumeration to `DbType.String`, but this behavior is only applicable to the non-model-based operations.

Let us say you have these enums.

```csharp
public enum CustomerType
{
    Direct,
    Indirect
}

public enum CustomerStatus
{
    Active,
    InActive
}
```

Then, the code below is forcing all the enumeration properties to be converted to `DbType.Int32` instead of `DbType.String`.

```csharp
// Set the Default Conversion for Enums
Converter.EnumDefaultDatabaseType = DbType.Int32;

// Non-Model Based Method Call
using (var connection = new SqlConnection(connectionString))
{
    var sql = "INSERT INTO [dbo].[Customer] (Name, CustomerType, CustomerStatus) " +
        "VALUES (@Name, @CustomerType, @CustomerStatus); " +
        "SELECT SCOPE_IDENTITY();";
    var param = new
    {
        Name = "John Doe",
        CutomerType = CustomerType.Direct,
        CustomerStatus = CustomerStatus.Active
    };
    var id = connection.ExecuteScalar<long>(sql, param);
}
```

{: .important }
> Please be reminded that this setting does not apply to the model-based operations. The library will automatically utilize the schema definition from the database when mapping the enumeration if the model-based operations are being used. In addition, this setting does not supercede the configured mappings on the specific property and/or type.

## DbNull Conversion

You can use the `DbNullToNull` method to convert the `DbDataReader` result into a `null` object if it the value is `DBNull.Value`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader())
    {
        var name = Converter.DbNullToNull(reader["Name"]);
    }
}
```

## Converting to Specific Type

You can use the `ToType` method to convert the type into a specific type. It first check if the type is equal, then it simply returns it, otherwise it is using the `System.ChangeType` method and cast it back to the target type.

```csharp
var value = "12345";
var converted = Converter.ToType<int>(value);
```