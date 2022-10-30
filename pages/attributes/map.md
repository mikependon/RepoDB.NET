---
layout: default
sidebar: attributes
title: "Map"
nav_order: 3
description: "Learn on how to use the RepoDB Map attribute."
permalink: /attribute/map
tags: [repodb, map]
parent: ATTRIBUTES
---

# Map

---

This is an attribute that is being used to map the class and its properties into its equivalent table and columns in the database.

## Class Mapping

Let us say you have a the table named `[department].[Person]` like below.

```csharp
CREATE TABLE [department].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [nvarchar](256) NOT NULL
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
}
```

> You can also use the mapping if the table name and the class name is not matching in terms of casing.

## Property Mapping

Let us say you have a the table named `[department].[Person]` like below.

```csharp
CREATE TABLE [dbo].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [LName] [nvarchar](128) NOT NULL,
    [LName] [nvarchar](128) NOT NULL
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
}
```

## Retrieval

You can use the [ClassMappedNameCache](/cacher/classmappednamecache) to retrieve the mappings for the class.

```csharp
var mappedName = ClassMappedNameCache.Get<Person>();
```

You can use either of the following.

- The `Get()` method of the [PropertyMappedNameCache](/cacher/propertymappednamecache)
- The `GetMappedName()` method of the [ClassProperty](/class/classproperty)

```csharp
var properties = PropertyCache.Get<Person>();

// Iterate the properties
properties
    .AsList()
    .ForEach(property =>
    {
        var mappedName = property.GetMappedName(); // or PropertyMappedNameCache.Get(property.PropertyInfo);
    });
```







