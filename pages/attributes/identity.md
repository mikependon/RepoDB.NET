---
layout: default
sidebar: attributes
title: "Identity"
nav_order: 2
description: "Learn on how to use the RepoDB Identity attribute."
permalink: /attribute/identity
tags: [repodb, identity]
parent: ATTRIBUTES
---

# Identity

---

This attribute marks a class property as an identity property.

Given a table `[dbo].[Person]` where `Id` is an identity field:

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
    [Identity] // Identity decoration
    public long Id { get; set; }
    public string Name { get; set; }
}
```

{: .note }
> Explicitly setting this attribute overrides the library's auto-identification logic. If applied to a property that is not a database identity, the library will use that property instead, which may cause some operations to fail.

To retrieve the identity property, use the [IdentityCache](/cacher/identitycache) object.

```csharp
var identity = IdentityCache.Get<Person>();
```

Or by .NET CLR type:

```csharp
var identity = IdentityCache.Get(typeof(Person));
```





