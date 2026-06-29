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

This attribute marks a class property as the primary property.

Given a table `[dbo].[Person]` where `Id` is the primary field:

```csharp
CREATE TABLE [dbo].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [nvarchar](128) NOT NULL
)
ON [PRIMARY];
GO
```

And a `Person` model class:

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

{: .note }
> Setting this attribute overrides the library's auto-identification logic. If applied to a property that is not a database primary key, the library will use that property instead, which may cause some operations to fail.

To retrieve the primary property, use the [PrimaryCache](/cacher/primarycache) object.

```csharp
var primary = PrimaryCache.Get<Person>();
```

Or by .NET CLR type:

```csharp
var primary = PrimaryCache.Get(typeof(Person));
```





