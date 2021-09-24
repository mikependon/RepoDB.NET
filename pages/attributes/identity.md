---
layout: default
sidebar: attributes
title: "Identity"
nav_order: 2
description: "Learn on how to use the RepoDB Identity attribute."
permalink: /attribute/identity
tags: [repodb, class, identity, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: ATTRIBUTES
---

# Identity

---

This is an attribute that is being used to mark the property of the class as an identity property.

### Usability

Let us say you have a the table named `[dbo].[Person]` where the field `Id` is an identity.

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
    [Identity] // Identity decoration
    public long Id { get; set; }
    public string Name { get; set; }
}
```

> By explicitly setting this attribute to any class property, you had overriden the auto-identification logic of the library. If you place this attribute in a property that is not really an identity from the database, then the library will use that property instead. By doing so, it may fail some of the operations.

### Retrieval

To retrieve the identity property, you can use the [IdentityCache](/cacher/identitycache) object.

```csharp
var identity = IdentityCache.Get<Person>();
```

Or via .NET CLR type.

```csharp
var identity = IdentityCache.Get(typeof(Person));
```





