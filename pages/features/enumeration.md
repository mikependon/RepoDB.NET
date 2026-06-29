---
layout: default
sidebar: features
title: "Enumeration"
description: "This is a feature that enables you to work with enumerations within the class objects."
permalink: /feature/enumeration
tags: [repodb, enumeration]
parent: FEATURES
---

# Enumeration

---

This feature enables working with enumeration objects within class properties. The library supports various transformation modes for enumerations.

## Property String

Given a table named `[dbo].[Person]` with the following structure:

```csharp
CREATE TABLE [dbo].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [nvarchar](128) NOT NULL,
    [Gender] [nvarchar](16) NOT NULL,
    [CreatedDateUtc] [datetime2](5) NOT NULL,
    CONSTRAINT [CRIX_Person_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

Define the enumeration:

```csharp
public enum Gender
{
    Unknown,
    Male,
    Female
}
```

Map it to the `Gender` column of the `Person` class:

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    public Gender Gender { get; set; } // Enumeration (As String)
    public DateTime CreatedDateUtc { get; set; }
}
```

{: .note }
> Enumeration values are saved in the database as [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0).

## Property String (As Int)

To force the value to be saved as [int](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types), use the [TypeMap](/attribute/typemap) attribute.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    [TypeMap(DbType.Int32)]
    public Gender Gender { get; set; } // Enumeration (As Forced Int)
    public DateTime CreatedDateUtc { get; set; }
}
```

Or via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Type<Gender>()
    .DbType(DbType.Int32);
```

Or via [TypeMapper](/mapper/typemapper):

```csharp
TypeMapper
    .Map(typeof(Gender), DbType.Int32);
```

## Property Int

Given a table with an integer `Gender` column:

```csharp
CREATE TABLE [dbo].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [nvarchar](128) NOT NULL,
    [Gender] [int] NOT NULL,
    [CreatedDateUtc] [datetime2](5) NOT NULL,
    CONSTRAINT [CRIX_Person_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

Define the enumeration:

```csharp
public enum Gender
{
    Unknown,
    Male,
    Female
}
```

Map it to the `Gender` column of the `Person` class:

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    public Gender Gender { get; set; } // Enumeration (As Int)
    public DateTime CreatedDateUtc { get; set; }
}
```

{: .note }
> Enumeration values are saved in the database as [int](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types).

## Default Conversion

By default, the library uses [DbType.String](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0) for all enumeration conversions in non-model-based operations (e.g., [ExecuteScalar](/operation/executescalar), [ExecuteNonQuery](/operation/executenonquery), and [ExecuteReader](/operation/executereader)). Override this behavior by setting `EnumDefaultDatabaseType` to any [DbType](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0).

```csharp
GlobalConfiguration
    .Setup(new()
    {
        EnumDefaultDatabaseType = DbType.Int32
    });
```

> For model-based operations (e.g., [Query](/operation/query), [Update](/operation/update), [Merge](/operation/merge)), the default conversion is not applied — the correct database type is inferred from the underlying table schema.

## PropertyHandler

A property handler can be created to manually control enumeration transformation by implementing [IPropertyHandler](/interface/ipropertyhandler).

```csharp
public class PersonGenderPropertyHandler : IPropertyHandler<string, Gender?>
{
    public Gender? Get(string input, PropertyHandlerGetOptons options)
    {
        if (!string.IsNullOrEmpty(input))
        {
            return (Gender)Enum.Parse(typeof(Gender), input);
        }
        return null;
    }

    public string Get(Gender? input, PropertyHandlerSetOptons options)
    {
        return input?.ToString();
    }
}
```

Map it via the [PropertyHandler](/attribute/propertyhandler) attribute:

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    [PropertyHandler(typeof(PersonGenderPropertyHandler))]
    public Gender Gender { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

{: .important }
> Enumeration auto-mapping is bypassed when a property handler is mapped to an enumeration property. Using a property handler gives you full control over the transformation.

## Query Expression

Enumerations can be used directly in query expressions.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var females = connection.Query<Person>(e => e.Gender == Gender.Female);
}
```

Or in raw SQL:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new
    {
        Gender = Gender.Female
    };
    var females = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE [Gender] = @Gender;", param);
}
```

## Type Inference

Type inference is supported, allowing you to infer an enumeration directly when fetching rows via [ExecuteQuery](/operation/executequery).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var genders = connection.ExecuteQuery<Gender>("SELECT [Gender] FROM [dbo].[Person];");
}
```
