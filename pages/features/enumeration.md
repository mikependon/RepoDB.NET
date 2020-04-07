---
layout: navpage
sidebar: features
title: "Enumeration (RepoDb)"
permalink: /feature/enumeration
tags: [repodb, class, enumeration, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Enumeration

This is the feature that enables you to work with enumerations within the class objects. The library supports various kind of transformation for enumerations.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Property String

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

Then you can create this enumeration.

```csharp
public enum Gender
{
    NotDefined,
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

> The enumeration values will be saved in the database as `String`.

#### Property String (As Int)

You can force the value to be saved as `String` type if you are using the [TypeMap](/attribute/typemap).

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

Or you can also use the type-level mapping via [TypeMapper](/mapper/typemapper) class.

```csharp
TypeMapper.Map(typeof(Gender), DbType.Int32);
```

#### Property Int

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

Then you can create this enumeration.

```csharp
public enum Gender
{
    NotDefined,
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

> The enumeration values will be saved in the database as `Integer`.

#### PropertyHandler

You can as well create a property handler to manually handle the enumerations transformation by implementing the [IPropertyHandler](/interface/ipropertyhandler).

```csharp
public class PersonGenderPropertyHandler : IPropertyHandler<Gender?, string>
{
    public Gender? Get(string input, ClassProperty property)
    {
        if (!string.IsNullOrEmpty(input))
        {
            return (Gender)Enum.Parse(typeof(Gender), input);
        }
        return null;
    }
    
    public string Get(Gender? input, ClassProperty property)
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

> The enumeration auto-mapping is being disregard if you have the property handler mapped into the enumeration property. By using the property handler, you have a lot of control as a developer about the transformation.

#### Query Expression

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
    var females = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE [Gender] = @Gender;", new { Gender = Gender.Female });
}
```
