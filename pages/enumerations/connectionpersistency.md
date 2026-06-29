---
layout: default
sidebar: enumerations
title: "ConnectionPersistency"
description: "A enumeration that defines the persistency of the database connection object when working with repository."
permalink: /enumeration/connectionpersistency
tags: [repodb, connectionpersistency]
parent: ENUMERATIONS
---

# ConnectionPersistency

---

This enum defines the persistency of the `DbConnection` instance within the [BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository) objects.

{: .note }
> This enumeration is useful when fine-grained control over how a repository instantiates its connection object is required.

## Enum Values

| Name | Description |
|:-----|:------------|
| PerCall | In every method call, a new `DbConnection` object is being used. This is the default setting. |
| Instance | A single instance of `DbConnection` object is used all throughout the lifespan of the repository. |

## Usability

Pass the connection persistency value in the constructor when instantiating a repository object.

The following example creates a `PersonRepository` that inherits [BaseRepository](/class/baserepository) and shares a single `DbConnection` instance across all calls.

```csharp
// Repository
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(string connectionString)
        : base(connectionString, ConnectionPersistency.Instance)
    { }

    ...
}

// Instantiation
using (var repository = new PersonRepository(connectionString))
{
    ...
}
```

The same applies to a [DbRepository](/class/dbrepository) object.

```csharp
// Repository
public class PersonRepository : DbRepository<SqlConnection>
{
    public PersonRepository(string connectionString)
        : base(connectionString, ConnectionPersistency.Instance)
    { }

    ...
}

// Instantiation
using (var repository = new PersonRepository(connectionString))
{
    ...
}
```
