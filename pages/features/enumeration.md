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

This is a feature that enables you to work on enumeration objects within the class object (property). The library supports various kind of transformation for enumerations.

## Property String

Let us say you have a table named `[dbo].[Person]` with the following structure.

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

Then, you can create this enumeration.

```csharp
public enum Gender
{
    Unknown,
    Male,
    Female
}
```

And map it to the `Gender` column of the `Person` class.

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
> The enumeration values will be saved in the database as `string`.

## Property String (As Int)

You can force the value to be saved as `int` type if you are using the [TypeMap](/attribute/typemap).

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

Or, via [FluentMapper](/mapper/fluentmapper) class.

```csharp
FluentMapper
    .Type<Gender>()
    .DbType(DbType.Int32);
```

Or, via [TypeMapper](/mapper/typemapper) class.

```csharp
TypeMapper
    .Map(typeof(Gender), DbType.Int32);
```

## Property Int

Let us say you have a table named `[dbo].[Person]` with the following structure.

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

Then, you can create this enumeration.

```csharp
public enum Gender
{
    Unknown,
    Male,
    Female
}
```

And map it to the `Gender` column of the `Person` class.

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
> The enumeration values will be saved in the database as `int`.

## Default Conversion

By default, the library is using the `DbType.String` as a conversion to all enumerations if being used to the non-model based operations (i.e.: [ExecuteScalar](/operation/executescalar), [ExecuteNonQuery](/operation/executenonquery) and [ExecuteReader](/operation/executereader)). You can override this behavior by simply setting the `EnumDefaultDatabaseType` to any `DbType`.

```csharp
GlobalConfiguration
    .Setup(new()
    {
        EnumDefaultDatabaseType = DbType.Int32
    });
```

> If you call any model-based operations (i.e.: [Query](/operation/query), [Update](/operation/update), [Merge](/operation/merge), etc), the default conversion is not used as it has already projected the correct database type to be passed/parsed based the underlying table schema.

## PropertyHandler

You can as well create a property handler to manually handle the enumerations transformation by implementing the [IPropertyHandler](/interface/ipropertyhandler).

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

And then map it via [PropertyHandler](/attribute/propertyhandler).

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
> The enumeration auto-mapping is being disregard if you have the property handler mapped into the enumeration property. By using the property handler, you have a lot of control as a developer about the transformation.

## Query Expression

You can as well use the enumeration in your query expression.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var females = connection.Query<Person>(e => e.Gender == Gender.Female);
}
```

Or even in the raw-SQL.

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

As type inference is supported by the library, you can as well infer the enumeration directly when fetching a row from the database via [ExecuteQuery](/operation/executequery).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var genders = connection.ExecuteQuery<Gender>("SELECT [Gender] FROM [dbo].[Person];");
}
```
