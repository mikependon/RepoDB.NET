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

This attribute maps a class and its properties to the corresponding table and columns in the database.

## Class Mapping

Given a table `[department].[Person]`:

```csharp
CREATE TABLE [department].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [nvarchar](256) NOT NULL
)
ON [PRIMARY];
GO
```

And a `Person` model class where the default schema does not match:

```csharp
[Map("[department].[Person]")] // Use the mapping as the default schema is [dbo]
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
}
```

{: .note }
> The mapping also applies when the table name and class name differ only in casing.

## Property Mapping

Given a table `[dbo].[Person]`:

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

And a `Person` model class where property names do not match the column names:

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

Use [ClassMappedNameCache](/cacher/classmappednamecache) to retrieve the class mapping.

```csharp
var mappedName = ClassMappedNameCache.Get<Person>();
```

For property-level mappings, use either of the following.

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







