---
layout: default
sidebar: operations
title: "BulkDelete"
permalink: /operation/mysql/bulkdelete
tags: [repodb, tutorial, bulkdelete, orm, hybrid-orm, mysql]
parent: "MySQL"
grand_parent: OPERATIONS
---

# BulkDelete

---

This method deletes rows from the database in bulk, matched by the defined qualifiers. It is supported for [RepoDb.MySql.BulkOperations](https://www.nuget.org/packages/RepoDb.MySql.BulkOperations), targeting the [MySql.Data](https://www.nuget.org/packages/MySql.Data) driver. This package also has a dedicated [BulkDeleteByKey](/operation/mysql/bulkdeletebykey) operation for deleting by primary key.

{: .note }
> This page documents the MySql.Data-specific arguments and examples. For the MySqlConnector implementation, see [BulkDelete (MySqlConnector)](/operation/mysqlconnector/bulkdelete); for SQL Server, see [BulkDelete (SQL Server)](/operation/sqlserver/bulkdelete); for Oracle, see [BulkDelete (Oracle)](/operation/oracle/bulkdelete).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkDelete| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Pseudo["Create Pseudo Table<br/>(Physical)"]
    Pseudo --> BulkCopy["MySqlBulkCopy<br/>(LOAD DATA LOCAL INFILE)"]
    BulkCopy -->|Write| PseudoTable[("Pseudo Table")]
    PseudoTable -->|"DELETE ... INNER JOIN<br/>ON qualifiers"| Table[("Target Table")]
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to delete rows at high speed. It leverages this package's internal `MySqlBulkCopy` class (`LOAD DATA LOCAL INFILE`-based, see [Operations (MySQL)](/operation/mysql)).

For deleting 1,000 or more rows, prefer this method over [DeleteAll](/operation/deleteall).

A pseudo (staging) table is created for the call. The library writes to it via its internal `MySqlBulkCopy`, then cascades the deletions to the target table via a `DELETE ... INNER JOIN` matched on the qualifiers — see [Operations (MySQL)](/operation/mysql) for the underlying mechanics.

## Special Arguments

The `qualifiers`, `bulkCopyTimeout`, `batchSize` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows, corresponding to the `ON` clause of the join. Defaults to the primary column if not specified.

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`pseudoTableType` (via `MySqlBulkImportPseudoTableType`) controls the kind of staging table used internally.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (MySQL)](/operation/mysql#pseudo-table-type) for details.

{: .note }
> `BulkDelete` only ever stages the qualifier columns (not the whole row) — it's the lightest of the entity/`DataTable`-based operations.

## Caveats

This operation creates a pseudo-temporary table for each call. The database user must have permission to create tables, or a `MySqlException` will be thrown. Because `CREATE TABLE`/`DROP TABLE` are DDL, each call implicitly commits any other pending work on the connection — see [The Staging Table Lifecycle](/operation/mysql#the-staging-table-lifecycle).

## Usability

The following example retrieves all inactive people, then bulk-deletes them from the `Person` table.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = connection.BulkDelete<Person>(people);
}
```

To specify a batch size:

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

#### DataTable

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var deletedRows = connection.BulkDelete("Person", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new MySqlConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new MySqlConnection(destinationConnectionString))
    {
        var deletedRows = destinationConnection.BulkDelete("Person", result);
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new MySqlConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM Person"))
    {
        using (var destinationConnection = new MySqlConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkDelete("Person", reader);
        }
    }
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var deletedRows = connection.BulkDelete("Person", people);
}
```

## Field Qualifiers

By default, the primary column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Async Method

An equivalent [BulkDeleteAsync](/operation/mysql/bulkdelete) method is also available.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = await connection.BulkDeleteAsync<Person>(people);
}
```
