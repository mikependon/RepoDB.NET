---
layout: default
sidebar: features
title: "Connection Persistency"
description: "This is a feature that enables you to manage the instance of the database connection object within the repository objects."
permalink: /feature/connectionpersistency
tags: [repodb, connectionpersistency]
parent: FEATURES
---

# Connection Persistency

---

This is a feature that enables you to control the persistency of the database connection object/instance within the repository objects (i.e.: [BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository)).

> Please see the [ConnectionPersistency](/enumeration/connectionpersistency) enumeration for more information.

## Types of Persistence

Below is the list of persistence supported by this library.

| Name         | Description  | 
|:-------------|:-------------|
| PerCall | In every method call, a new connection object is being used. This is the default setting. |
| Instance | A single instance of connection object is used all throughout the lifespan of the repository. |

## Repository

If you are working with [BaseRepository](/class/baserepository) object, you have to pass it on the constructor of your derived repository.

```csharp
// Repository
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(string connectionString,
        ConnectionPersistency persistency)
        : base(connectionString, persistency)
    { }

    ...
}

// Instantiation
using (var repository = new PersonRepository(connectionString, ConnectionPersistency.Instance))
{
    ...
}
```

You should do the same if you are to use the [DbRepository](/class/dbrepository) object.

```csharp
// Repository
public class NorthwindRepository : DbRepository<SqlConnection>, INorthwinRepository
{
    public NorthwindRepository(string connectionString,
        ConnectionPersistency persistency)
        : base(connectionString, persistency)
    { }

    ...
}

// Instantiation
using (var repository = new NorthwindRepository(connectionString, ConnectionPersistency.Instance))
{
    ...
}
```

Or, you can directly pass and use it right away.

```csharp
using (var repository = new DbRepository<SqlConnection>(settings.Value.ConnectionString, ConnectionPersistency.Instance))
{
    var people = repository.QueryAll<Person>();
}
```

## Create Connection Method

It creates a connection object that can be utilized within the repository object. Its behaviour will be based on the type of the persistency you had chosen. If it is used with `PerCall`, it always returns a new instance of connection object. If it is used with `Instance`, it always return the already created instance of connection object within the repository. However, such behavior will be superceded if the `force` argument is enforced (a value of `true`), as the new instance of connection object is always being created.

## Dispose Process

If you are using the `PerCall`, the instance of the connection object is always being disposed right after the call to any operation. However, if you are using the `Instance`, the active instance of the connection object is only being disposed if the parent repository object has been disposed. Therefore, it is always important to call the `Dispose()` method of the repository if you are using the `Instance` persistency and finished using it, to ensure avoid an orphaned open connection towards the database.

> Please be aware of when to call the repository `Dispose()` method, otherwise it may behave unexpectedly.