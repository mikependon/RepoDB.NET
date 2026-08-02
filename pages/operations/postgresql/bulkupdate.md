---
layout: default
sidebar: operations
title: "BulkUpdate"
permalink: /operation/postgresql/bulkupdate
tags: [repodb, tutorial, bulkupdate, orm, hybrid-orm, sqlserver]
parent: "PostgreSQL"
grand_parent: OPERATIONS
---

# BulkUpdate

---

{: .warning }
> This method was previously named `BinaryBulkUpdate`. That name is now deprecated in favor of `BulkUpdate` and will be tagged starting v1.16.0 of the `RepoDb.PostgreSql` and `RepoDb.PostgreSql.BulkOperations` packages.

This method updates existing rows in the database in bulk. It is supported only for [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkUpdate| Reader["DbDataReader<br/>IEnumerable&lt;T&gt;<br/>DataTable"]
    Reader -->|Pass| BinaryImport["BinaryImport"]
    BinaryImport -->|Write| Decision{"PseudoTableType<br/>Physical?"}
    Decision -->|YES| Physical["Create Table<br/>(Physical)"]
    Decision -->|NO| Temporary["Create Table<br/>(Temporary)"]
    Physical -->|"UPDATE (SQL)"| Table[("Table")]
    Temporary -->|"UPDATE (SQL)"| Table
```

## Use Case

Use this method to update rows at high speed. It leverages the native bulk operation from the Npgsql library via the [NpgsqlBinaryImporter](https://www.npgsql.org/doc/api/Npgsql.NpgsqlBinaryImporter.html) class.

For updating 1,000 or more rows, prefer this method over [UpdateAll](/operation/updateall).

## Special Arguments

The `qualifiers` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the qualifier fields used in the operation, corresponding to the WHERE clause. Defaults to the primary column if not specified.

`pseudoTableType` controls whether a physical pseudo-table is created during the operation. Defaults to a temporary table.

{: .important }
> It is highly recommended to use the [PostgreSqlBulkImportPseudoTableType.Temporary](/enumeration/postgresql/postgresqlbulkimportpseudotabletype#temporary) value in the `pseudoTableType` argument when working with parallelism.

## Usability

Pass the list of entities to the operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var updatedRows = connection.BulkUpdate<Person>(people);
}
```

{: .note }
> It returns the number of rows updated in the underlying table.

To specify a batch size:

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var updatedRows = connection.BulkUpdate<Person>(people, batchSize: 100);
}
```

{: .important }
> If `batchSize` is not set, all items in the collection are sent at once.

To target a specific table, pass the literal table name.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate("[dbo].[Person]", people);
}
```

#### DataTable

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var table = ConvertToDataTable(people);
    var updatedRows = connection.BulkUpdate("[dbo].[Person]", table);
}
```

#### Dictionary/ExpandoObject

```csharp
var people = GetPeopleAsDictionary(1000);

using (var connection = new NpgsqlConnection(destinationConnectionString))
{
    var updatedRows = connection.BulkUpdate("[dbo].[Person]", people);
}
```

#### DataReader

```csharp
using (var sourceConnection = new NpgsqlConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        using (var destinationConnection = new NpgsqlConnection(destinationConnectionString))
        {
            var updatedRows = destinationConnection.BulkUpdate("[dbo].[Person]", reader);
        }
    }
}
```

Or via [DataEntityDataReader](/class/dataentitydatareader).

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var updatedRows = connection.BulkUpdate("[dbo].[Person]", reader);
    }
}
```

## Field Qualifiers

By default, the primary column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Physical Temporary Table

To use a physical pseudo-temporary table, pass [PostgreSqlBulkImportPseudoTableType.Temporary](/enumeration/postgresql/postgresqlbulkimportpseudotabletype#physical) in the `pseudoTableType` argument.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var updatedRows = connection.BulkUpdate("[dbo].[Person]",
        people,
        pseudoTableType: PostgreSqlBulkImportPseudoTableType.Physical);
}
```

{: .note }
> A physical pseudo-temporary table improves performance over a standard temporary table, but is shared across all calls. Parallelism may fail in this scenario.

