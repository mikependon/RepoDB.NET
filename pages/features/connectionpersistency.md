---
layout: page
title: "Connection Persistency (RepoDb)"
permalink: /feature/connectionpersistency
tags: [repodb, class, connectionpersistency, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Connection Persistency

This is the feature that enables you to manage the instance of the `DbConnection` object within the repository objects (ie: [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository)). Please see the [ConnectionPersistency](/enumeration/connectionpersistency) enumeration for more information.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

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

You can do the same with [DbRepository](/class/dbrepository).

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

This method behaves differently based on the type of the persistency you had chosen.

When using the `PerCall`, calling this method will always create and return a new instance of `IDbConnection` object.

When using the `Instance`, calling this method will always return the already created instance of `IDbConnection` object. However, by passing the value of `true` in the `force` argument, a new instance of `IDbConnection` object is being returned. 

#### Dispose Process

If you are using the `PerCall`, the instance of the `IDbConnection` object is always being disposed right after the method call.

By using the `Instance`, the active instance of the `IDbConnection` object is only being disposed when the repository object is being disposed.

> Please be aware of when to call the repository `Dispose()` method when you are using the `Instance`, otherwise it will behaved unexpectedly.