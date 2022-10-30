---
layout: default
sidebar: attributes
title: "Primary"
nav_order: 4
description: "Learn on how to use the RepoDB Primary attribute."
permalink: /attribute/primary
tags: [repodb, primary]
parent: ATTRIBUTES
---

# Primary

---

This is an attribute that is being used to mark the property of the class as a primary property.

Let us say you have a the table named `[dbo].[Person]` where the field `Id` is an primary.

```csharp
CREATE TABLE [dbo].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [nvarchar](128) NOT NULL
)
ON [PRIMARY];
GO
```

And a model class named `Person` like below.

```csharp
public class Person
{
    [Primary] // Primary decoration
    public long Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

> By setting this attribute to any class property, you had overriden the auto-identification logic of the library. If you place this attribute in a property that is not really a primary from the database, then the library will use that property instead. By doing so, it may fail some of the operations.

To retrieve the primary property, you can use the [PrimaryCache](/cacher/primarycache) object.

```csharp
var primary = PrimaryCache.Get<Person>();
```

Or via .NET CLR type.

```csharp
var primary = PrimaryCache.Get(typeof(Person));
```





