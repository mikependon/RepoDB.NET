---
layout: navpage
sidebar: features
title: "Connection Persistency"
description: "This is a feature that enables you to manage the instance of the database connection object within the repository objects."
permalink: /feature/connectionpersistency
tags: [repodb, class, connectionpersistency, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Connection Persistency

This is a feature that enables you to control the persistency of the IDbConnection instance within the repository objects (aka: [BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository)). Please see the [ConnectionPersistency](/enumeration/connectionpersistency) enumeration for more information.

#### Types of Persitency

- `PerCall (Default)` - in every method call, a new `DbConnection` object is being used.
- `Instance` - a single instance of `DbConnection` object is used all throughout the lifespan of the repository.

#### Repository

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

You can do the same with [DbRepository](/class/dbrepository) object.

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

Or, you can directly pass it and use along the way.

```csharp
using (var repository = new DbRepository<SqlConnection>(settings.ConnectionString, ConnectionPersistency.Instance))
{
    var people = repository.QueryAll<Person>();
}
```

#### CreateConnection Method

This method behaves differently based on the type of the persistency you had chosen. If it is used with `PerCall`, it always returns a new instance of `IDbConnection` object. If it is used with `Instance`, it always return the already created instance of `IDbConnection` object.

Simply set the value of `force` argument to  `true` if you wish to always return a new instance of `IDbConnection` object. 

#### Dispose Process

If you are using the `PerCall`, the instance of the `IDbConnection` object is always being disposed right after the method call. However, if you are using the `Instance`, the active instance of the `IDbConnection` object is only being disposed when the parent repository object is being disposed. Please be aware of when to call the repository `Dispose()` method, otherwise it may behave unexpectedly.