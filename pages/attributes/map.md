---
layout: navpage
sidebar: attributes
title: "Map"
description: "Learn on how to use the RepoDB Map attribute."
permalink: /attribute/map
tags: [repodb, class, map, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Map

This is an attribute that is being used to map the class and its properties into its equivalent table and columns in the database.

#### Class Mapping

Let us say you have a the table named `[department].[Person]` like below.

```csharp
CREATE TABLE [department].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [Age] [int] NOT NULL,
    [DateInsertedUtc] [datetime2](5) NOT NULL,
    CONSTRAINT [CRIX_Person_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

And a model class named `Person` like below in which some of the column is not matching.

```csharp
[Map("[department].[Person]")] // Use the mapping as the default schema is [dbo]
public class Person
{
public long Id { get; set; }
public string Name { get; set; }
public int Age { get; set; }
public DateTime CreatedDateUtc { get; set; }
}
```

> You can also use the mapping if the table name and class name is not matching case-insensitively.

#### Property Mapping

Let us say you have a the table named `[department].[Person]` like below.

```csharp
CREATE TABLE [dbo].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [LName] [nvarchar](128) NOT NULL,
    [LName] [nvarchar](128) NOT NULL,
    [Age] [int] NOT NULL,
    [DateInsertedUtc] [datetime2](5) NOT NULL,
    CONSTRAINT [CRIX_Person_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

And a model class named `Person` like below in which some of the columns are not matching.

```csharp
public class Person
{
    public long Id { get; set; }
    [Map("FName")]
    public string FirstName { get; set; }
    [Map("LName")]
    public string LastName { get; set; }
    public int Age { get; set; }
    [Map("DateInsertedUtc")]
    public DateTime CreatedDateUtc { get; set; }
}
```

#### Get the Class Mapping

You can use the [ClassMappedNameCache](/cacher/classmappednamecache) to extract the mappings for the class.

```csharp
var mappedName = ClassMappedNameCache.Get<Person>();
```

#### Get the Property Mapping

You can use the [PropertyMappedNameCache](/cacher/propertymappednamecache) to extract the mappings for the `PropertyInfo`.

```csharp
var properties = PropertyCache.Get<Person>();

// Iterate the properties
properties
    .AsList()
    .ForEach(property =>
    {
        var mappedName = PropertyMappedNameCache.Get(property.PropertyInfo);
    });
```







