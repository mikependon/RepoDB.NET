---
layout: post
title: "RepoDB version 1.12.3 - Bug and Hot Fixes for version 1.12.0"
author: "Michael Camara Pendon"
date: 2020-10-03 06:00:00 +0200
categories: blogs repodb
---

When we announced the GA of [RepoDB v1.12.0](https://www.nuget.org/packages/RepoDb/1.12.0), many users were not able to pre-test the solutions even by having the 4 betas for the last 2 months. This has unfortunately leads them to file the issues/bugs right after the releases.

The version [RepoDB v1.12.3](https://www.nuget.org/packages/RepoDb/1.12.3) is a version that contains the fixes to those issues/bugs.

Allow us to explain and enumerate those issues.

### Possible Compiler Collision Problem

Only in the new compiler. In order for us to ensure that the collision will not happen on the caching of the AOT Compiled Expression, we need to consider the caching of the following.

- HashCode of the Type
- HashCode of the Name of each DbDataReader Fields
- HashCode of the Type of each DbDataReader Fields
- HashCode of the Ordinal of each DbDataReader Fields - this is not yet there

Without the ordinal caching, the collision may happen in various queries per entity model, ended-up multiple invalid casting exception (and/or data assignment). See below.

```csharp
var people = connection.ExecuteQuery<Person>("SELECT Id, Name FROM Person;");
people = connection.ExecuteQuery<Person>("SELECT Name, Id FROM Person;");
```

This is critical, therefore, we had issued the fix immediately.

### Fetch Async Operations

Affected by the latest release and core compiler updates, the current fetch operations are using the [DbDataReader.ToEnumerable()](https://github.com/mikependon/RepoDB/blob/4361bebbcb947340ad1bc614b652e69fdc628be0/RepoDb.Core/RepoDb/Reflection/DataReader.cs#L24) operations. Therefore, all the Async fetch operations (i.e.: [BatchQueryAsync](/operation/batchoperation), [QueryAsync](/operation/query) and [ExecuteQueryAsync](/operation/executequery)) are not truly async methods.


As a fix, we again reverted the code to [DbDataReader.ToEnumerableAsync()](https://github.com/mikependon/RepoDB/blob/4361bebbcb947340ad1bc614b652e69fdc628be0/RepoDb.Core/RepoDb/Reflection/DataReader.cs#L53) and made an updates on the mentioned operations to have the proper chain of calls.

### NullException on Empty WHERE Expressions

As reported by the user, a `NullReferenceException` is thrown if the [Query](/operation/query) Expression is being equate to the `NULL` values.

```csharp
connection.Query<Person>(e => e.Name == null);
```

The issue was happened is because of the changes made on the latest compiler. In addition to that, we had added the proper Integration Tests to cover this case.

### Behavior change from Previous Versions

In the previous version, the code below is working for the table that has no `Primary` and `Identity` key.

```csharp
var model = new { Id = 1, ColumnToUpdate = "Value" };
connection.Update("NonKeyedTable", model);
```

It was affected by the changes during the updates on the core compiler, therefore, we need to issue an immediate hotfix towards it.

### Additional Requests

A shared POCO between different data provider is not working if the DB Specific provider attribute is defined.

Let us say, you would like to use the model below to both PostgreSQL and SQLite.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    [NpgsqlTypeMap(NpgsqlDbType.Json)]
    public string ExtraInformation { get; set; }
}
```

As stated with the attribute [NpgsqlTypeMap](/attribute/npgsqltypemap), such model is catered for PostgreSQL, however, a user also would like to use such model for SQLite, and there the exception is being thrown.

This has been a part of the latest release as well.

### Closing Note

You can visit the actual [release](https://github.com/mikependon/RepoDB/releases/tag/v1.12.3) from our Github page.