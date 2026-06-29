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

The main converter class for the library. It exposes mutable properties that affect how the library converts data when reading from or writing to the database.

## Properties

| Name | Description |
|:-----|:------------|
| ConversionType | The conversion type used when converting a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) into .NET CLR types. Defaults to [ConversionType.Default](/enumeration/conversiontype). |
| EnumDefaultDatabaseType | The default database type for enumerations when used as parameters in non-entity-based operations ([ExecuteScalar](/operation/executescalar), [ExecuteNonQuery](/operation/executenonquery), [ExecuteReader](/operation/executereader)). |

Deprecated
{: .label .label-red }

## Methods

| Name | Description |
|:-----|:------------|
| DbNullToNull | Converts `DBNull.Value` to `null`. |
| ToType | Returns the value as-is if the type matches the target; otherwise converts it. |

## Setting to Automatic Conversion

To enable automatic conversion between .NET CLR types and database types, set `ConversionType` to `Automatic`.

```csharp
Converter.ConversionType = ConversionType.Automatic;
```

Deprecated
{: .label .label-red }

{: .note }
> See the [ConversionType](/enumeration/conversiontype) enumeration for details.

## When to use the Enum Default Database Type?

Use this setting to override the default enumeration conversion behavior. By default, the library converts enumerations to [DbType.String](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0) for non-model-based operations.

Given the following enumerations:

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

The following forces all enumeration properties to be converted to `DbType.Int32`:

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
> This setting does not apply to model-based operations, where the library uses the database schema definition. It also does not override mappings configured at the property or type level.

## DbNull Conversion

Use `DbNullToNull` to convert a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) value to `null` when it is `DBNull.Value`.

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

Use `ToType` to convert a value to a specific type. If the types match, the value is returned directly; otherwise, `System.ChangeType` is used.

```csharp
var value = "12345";
var converted = Converter.ToType<int>(value);
```
