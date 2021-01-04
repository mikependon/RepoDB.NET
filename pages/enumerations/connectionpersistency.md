---
layout: default
sidebar: enumerations
title: "ConnectionPersistency"
description: "A enumeration that defines the persistency of the database connection object when working with repository."
permalink: /enumeration/connectionpersistency
tags: [repodb, class, connectionpersistency, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: ENUMERATIONS
---

# ConnectionPersistency

---

This is the enumeration used to define the persistency of the `DbConnection` instance within the [BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository) objects.

> This enumueration is very useful when you are on a situation that you do need to control the way how repository instantiate a connection object.

### Enum Values

- `PerCall` - in every method call, a new `DbConnection` object is being used. This is the default setting.
- `Instance` - a single instance of `DbConnection` object is used all throughout the lifespan of the repository.

### How to use?

Simply pass the connection persistency value in the constructor when instantiating a repository object.

Let us say, you create a repository object named `Person` that inherits the [BaseRepository](/class/baserepository) and you would like to share a single `DbConnection` object in all calls.

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

Or for [DbRepository](/class/dbrepository) object.

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