---
layout: navpage
sidebar: features
title: "Field/Class Mapping"
description: "This is the feature that would allow you to map the class (or a property) into its equivalent object in the database."
permalink: /feature/fieldclassmapping
tags: [repodb, class, fieldmapping, classmapping, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Field/Class Mapping

This is the feature that would allow you to map the class (or a property) into its equivalent object in the database. It uses the [Map](/attribute/map) attribute.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Mapping a Class

Let us say you have the table below that resides on a different schema.

```csharp
CREATE TABLE [sales].[Customer]
(
	[Id] [bigint] IDENTITY(1,1) NOT NULL,
	[Name] [nvarchar](128) NOT NULL,
	[Age] [int] NOT NULL,
	[CreatedDateUtc] [datetime2](5) NOT NULL,
	CONSTRAINT [CRIX_Customer_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

By default, the SQL Server is using the `dbo` as the default schema. You can solve this by using the correct mapping.

```csharp
[Map("[sales].[Customer]")]
public class Customer
{
    public long Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

You can also map if the table name is not equals to the class name.

#### Mapping a Property

Let us say you have the table below with non-descriptive columns.

```csharp
CREATE TABLE [dbo].[Supplier]
(
	[Id] [bigint] IDENTITY(1,1) NOT NULL,
	[LName] [nvarchar](128) NOT NULL,
	[FName] [nvarchar](128) NOT NULL,
	[Location] [nvarchar](256) NOT NULL,
	[CreatedDateUtc] [datetime2](5) NOT NULL,
	CONSTRAINT [CRIX_Supplier_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

And you would like to name the class properties like below.

```csharp
public class Supplier
{
    public long Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Address { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

Since the `FName` column and `FirstName` property is not matching, then you can solve it via mapping (same goes for the other fields). Please see the code snippets below.

```csharp
public class Supplier
{
    public long Id { get; set; }
    [Map("FName")]
    public string FirstName { get; set; }
    [Map("LName")]
    public string LastName { get; set; }
    [Map("Location")]
    public string Address { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

#### Retrieving the Class Mapped Name

To retrieve a mapped name, use the [ClassMappedNameCache](/cacher/classmappednamecache) class.

```csharp
var mappedName = ClassMappedNameCache.Get<Customer>(); // will return '[sales].[Customer]' rather than 'Customer'
```

#### Retrieving the Property Mapped Name

To retrieve a mapped name, use the combination of [PropertyCache](/cacher/propertycache) to get the list of properties and [PropertyMappedNameCache](/cacher/propertymappednamecache) class to get the mapping.

```csharp
var properties = PropertyCache.Get<Customer>().AsList();
properties.ForEach(
    p => PropertyMappedNameCache.Get(p)); 
```

> We highly recommend to use the cache classes so the already-created objects will not be created again. It helps your application run faster and more efficient.