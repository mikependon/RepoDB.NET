---
layout: default
sidebar: operations
title: "BulkDelete"
permalink: /operation/oracle/bulkdelete
tags: [repodb, tutorial, bulkdelete, orm, hybrid-orm, oracle]
parent: "Oracle"
grand_parent: OPERATIONS
---

# BulkDelete

---

This method deletes rows from the database in bulk, matched by the defined qualifiers. It is supported for [Oracle](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations). Oracle also has a dedicated [BulkDeleteByKey](/operation/oracle/bulkdeletebykey) operation for deleting by primary key.

{: .note }
> This page documents the Oracle-specific arguments and examples. For the SQL Server implementation, see [BulkDelete (SQL Server)](/operation/sqlserver/bulkdelete).

## Use Case

Use this method to delete rows at high speed. It leverages the native bulk operation from ODP.NET via the [OracleBulkCopy](https://docs.oracle.com/en/database/oracle/oracle-data-access-components/23.9/odpnt/OracleBulkCopyClass.html) class.

For deleting 1,000 or more rows, prefer this method over [DeleteAll](/operation/deleteall).

A pseudo (staging) table is created under a transaction context. The library writes to it via [BulkInsert](/operation/oracle/bulkinsert) internally, then cascades the deletions to the target table by matching on `ROWID` — see [Operations (Oracle)](/operation/oracle) for the underlying mechanics.

## Special Arguments

The `qualifiers`, `bulkCopyTimeout`, `batchSize` and `pseudoTableType` arguments are available for this operation.

`qualifiers` defines the fields used to match existing rows, corresponding to the `WHERE` clause. Defaults to the primary column if not specified.

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`pseudoTableType` (via [OracleBulkImportPseudoTableType](/enumeration/oracle/oraclebulkimportpseudotabletype)) controls the kind of staging table used internally.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (Oracle)](/operation/oracle#pseudo-table-type) for details.

## Caveats

This operation creates a pseudo-temporary table internally under a transaction context. The database user must have permission to create tables, or an `OracleException` will be thrown.

## Usability

The following example retrieves all inactive people, then bulk-deletes them from the `Person` table.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = connection.BulkDelete<Person>(people);
}
```

To specify a batch size:

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

#### DataTable

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var deletedRows = connection.BulkDelete("Person", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new OracleConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new OracleConnection(destinationConnectionString))
    {
        var deletedRows = destinationConnection.BulkDelete("Person", result);
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new OracleConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM Person"))
    {
        using (var destinationConnection = new OracleConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkDelete("Person", reader);
        }
    }
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var deletedRows = connection.BulkDelete("Person", people);
}
```

## Field Qualifiers

By default, the primary column is used as the qualifier. To override, pass a list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var deletedRows = connection.BulkDelete<Person>(people,
        qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

{: .important }
> Use indexed columns from the target table as qualifiers to maximize performance.

## Async Method

An equivalent [BulkDeleteAsync](/operation/oracle/bulkdelete) method is also available.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == false);
    var deletedRows = await connection.BulkDeleteAsync<Person>(people);
}
```
