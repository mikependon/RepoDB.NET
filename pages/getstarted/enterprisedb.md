---
layout: default
sidebar: getstarted
title: "EnterpriseDB"
description: "Learn on how to work with EnterpriseDB (EDB Postgres Advanced Server) databases using RepoDB library."
permalink: /tutorial/get-started-enterprisedb
tags: [repodb, tutorial, get-started, orm, hybrid-orm, enterprisedb, edb]
parent: GET STARTED
---

# Get Started for EnterpriseDB

---

RepoDB is a hybrid .NET ORM library for [EDB Postgres Advanced Server](https://www.enterprisedb.com/products/edb-postgres-advanced-server) (EnterpriseDB). The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/src/Providers/RepoDb.EnterpriseDb) and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

Support ships as two packages:

- [RepoDb.EnterpriseDb](https://www.nuget.org/packages/RepoDb.EnterpriseDb) — the core provider, built on the Npgsql-backed [RepoDb.Connector.EnterpriseDb](https://www.nuget.org/packages/RepoDb.Connector.EnterpriseDb) connector.
- [RepoDb.EnterpriseDb.BulkOperations](https://www.nuget.org/packages/RepoDb.EnterpriseDb.BulkOperations) — adds `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey`.

{: .note }
> `EDBConnection` throughout both packages refers to `RepoDb.Connector.EnterpriseDb.EDBConnection` — distinct from the official `EnterpriseDB.EDBClient` driver's connection type of the same name, which this provider does not use.

## Installation

Install the library via NuGet using the Package Manager Console.

```csharp
> Install-Package RepoDb.EnterpriseDb
```

After installation, call the globalized setup method to initialize all dependencies for EnterpriseDB.

```csharp
GlobalConfiguration
    .Setup()
    .UseEnterpriseDb();
```

To use bulk operations (`BulkDelete`, `BulkDeleteByKey`, `BulkInsert`, `BulkMerge` and `BulkUpdate` — see [Operations (EnterpriseDB)](/operation/enterprisedb)), install the [RepoDb.EnterpriseDb.BulkOperations](https://www.nuget.org/packages/RepoDb.EnterpriseDb.BulkOperations) package.

```csharp
> Install-Package RepoDb.EnterpriseDb.BulkOperations
```

## Create a Table

The examples below assume the following table exists in the database.

```csharp
CREATE TABLE "Person"
(
    "Id" BIGINT GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    "Name" TEXT,
    "Age" INTEGER,
    "CreatedDateUtc" TIMESTAMP
);
```

## Create a Model

The examples below assume the following model exists in the application.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

## Creating a Record

To insert a row, use the [Insert](/operation/insert) method.

```csharp
var person = new Person
{
    Name = "John Doe",
    Age = 54,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new EDBConnection(ConnectionString))
{
    var id = connection.Insert(person);
}
```

To insert multiple rows, use the [InsertAll](/operation/insertall) operation.

```csharp
var people = GetPeople(100);
using (var connection = new EDBConnection(ConnectionString))
{
    var rowsInserted = connection.InsertAll(people);
}
```

{: .note }
> The [Insert](/operation/insert) method returns the value of the identity/primary column, while [InsertAll](/operation/insertall) returns the number of rows inserted. Both methods automatically set the identity/primary property value back onto the entity model (if present).

## Querying a Record

To query a row, use the [Query](/operation/query) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var person = connection.Query<Person>(e => e.Id == 1);
    /* Process the result here */
}
```

To query all rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var people = connection.QueryAll<Person>();
    /* Process the results here */
}
```

## Merging a Record

To merge a row, use the [Merge](/operation/merge) method.

```csharp
var person = new Person
{
    Id = 1,
    Name = "John Doe",
    Age = 57,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new EDBConnection(ConnectionString))
{
    var id = connection.Merge(person);
}
```

By default, the primary column is used as a qualifier. Custom qualifiers can also be specified.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var id = connection.Merge(person, qualifiers: (p => new { p.Name }));
}
```

To merge multiple rows, use the [MergeAll](/operation/mergeall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new EDBConnection(ConnectionString))
{
    var affectedRecords = connection.MergeAll<Person>(people);
}
```

{: .note }
> The [Merge](/operation/merge) method returns the identity/primary column value, while [MergeAll](/operation/mergeall) returns the number of rows affected. Both methods automatically set the identity/primary property value back onto the entity (if present).

## Deleting a Record

To delete a row, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var deletedCount = connection.Delete<Person>(1);
}
```

Other columns can also be used as qualifiers.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

A list of primary keys can also be passed for targeted deletion.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var primaryKeys = new [] { 10045, 11001, ..., 12011 };
    var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

{: .note }
> Both [Delete](/operation/delete) and [DeleteAll](/operation/deleteall) return the number of rows affected.

## Updating a Record

To update a row, use the [Update](/operation/update) method.

```csharp
var person = new Person
{
    Id = 1,
    Name = "James Doe",
    Age = 55,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new EDBConnection(ConnectionString))
{
    var updatedRows = connection.Update<Person>(person);
}
```

Specific columns can also be targeted using a dynamic update.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var updatedRows = connection.Update("Person", new { Id = 1, Name = "James Doe" });
}
```

To update multiple rows, use the [UpdateAll](/operation/updateall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new EDBConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people);
}
```

By default, the primary column is used as a qualifier. Custom qualifiers can also be specified.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people,
        qualifiers: (p => new { p.Name }));
}
```

{: .note }
> Both [Update](/operation/update) and [UpdateAll](/operation/updateall) return the number of rows affected.

## Executing a Query

To execute a non-query statement, use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var sql = "DELETE FROM \"Person\" WHERE \"Id\" = @Id;";
    var affectedRecords = connection.ExecuteNonQuery(sql, new { Id = 1 });
}
```

To execute a query and return mapped objects, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var sql = "SELECT * FROM \"Person\" ORDER BY \"Id\" ASC;";
    var people = connection.ExecuteQuery<Person>(sql);
    /* Process the results here */
}
```

To execute a query and return a scalar value, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var sql = "SELECT MAX(\"Id\") FROM \"Person\";";
    var maxId = connection.ExecuteScalar<long>(sql);
}
```

To execute a query and return a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0), use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var sql = "SELECT * FROM \"Person\" ORDER BY \"Id\" ASC;";
    using (var reader = connection.ExecuteReader(sql))
    {
        /* Process the data reader here */
    }
}
```

## Executing a Stored Procedure

Call a `PROCEDURE` with `CALL` via the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var affectedRecords = connection.ExecuteNonQuery("CALL sp_update_person(@Id, @Name);",
        new { Id = 1, Name = "James Doe" });
}
```

Call a `FUNCTION` that returns rows with a `SELECT * FROM` wrapper via [ExecuteQuery](/operation/executequery).

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("SELECT * FROM fn_get_people();");
}
```

## Typed Result Execution

Single-column result sets can be mapped to any .NET CLR type via [ExecuteQuery](/operation/executequery).

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var sql = "SELECT \"Name\" FROM \"Person\";";
    var names = connection.ExecuteQuery<string>(sql);
}
```

{: .note }
> The resultset of this operation is an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object.
