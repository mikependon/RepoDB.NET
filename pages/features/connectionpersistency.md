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

This feature controls the persistence of the database connection object within the repository classes ([BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository)).

{: .note }
> Please see the [ConnectionPersistency](/enumeration/connectionpersistency) enumeration for more information.

## Types of Persistence

The following persistence modes are supported:

| Name         | Description  | 
|:-------------|:-------------|
| PerCall | A new connection object is used for every method call. This is the default setting. |
| Instance | A single connection object is shared for the lifetime of the repository. |

## Repository

When working with [BaseRepository](/class/baserepository), pass the persistency mode in the constructor of the derived repository.

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

The same applies to [DbRepository](/class/dbrepository).

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

Or pass it directly during instantiation:

```csharp
using (var repository = new DbRepository<SqlConnection>(settings.Value.ConnectionString, ConnectionPersistency.Instance))
{
    var people = repository.QueryAll<Person>();
}
```

## Create Connection Method

This method creates a connection object for use within the repository. Its behavior depends on the selected persistency mode. With `PerCall`, it always returns a new connection instance. With `Instance`, it returns the existing connection instance. This behavior is overridden if the `force` argument is set to `true`, which always creates a new connection instance.

## Dispose Process

With `PerCall`, the connection object is disposed immediately after each operation. With `Instance`, the connection is only disposed when the parent repository is disposed. Always call `Dispose()` on the repository when using `Instance` persistency to avoid orphaned open database connections.

{: .note }
> Be mindful of when the repository `Dispose()` method is called, as improper disposal may lead to unexpected behavior.
