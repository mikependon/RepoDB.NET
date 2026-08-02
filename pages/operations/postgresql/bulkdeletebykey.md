---
layout: default
sidebar: operations
title: "BulkDeleteByKey"
permalink: /operation/postgresql/bulkdeletebykey
tags: [repodb, tutorial, bulkdeletebykey, orm, hybrid-orm, sqlserver]
parent: "PostgreSQL"
grand_parent: OPERATIONS
---

# BulkDeleteByKey

---

{: .warning }
> This method was previously named `BinaryBulkDeleteByKey`. That name is now deprecated in favor of `BulkDeleteByKey` and will be removed starting v1.16.0 of the `RepoDb.PostgreSql` and `RepoDb.PostgreSql.BulkOperations` packages.

This method deletes rows from the database using a list of primary keys in bulk. It is supported only for [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkDeleteByKey| Reader["IEnumerable&lt;TPrimaryKey&gt;"]
    Reader -->|Pass| BinaryImport["BinaryImport"]
    BinaryImport -->|Write| Decision{"PseudoTableType<br/>Physical?"}
    Decision -->|YES| Physical["Create Table<br/>(Physical)"]
    Decision -->|NO| Temporary["Create Table<br/>(Temporary)"]
    Physical -->|"DELETE (SQL)"| Table[("Table")]
    Temporary -->|"DELETE (SQL)"| Table
```

## Use Case

Use this method to delete rows by primary key at high speed. It leverages the native bulk operation from the Npgsql library via the [NpgsqlBinaryImporter](https://www.npgsql.org/doc/api/Npgsql.NpgsqlBinaryImporter.html) class.

## Special Arguments

The `pseudoTableType` argument controls whether a physical pseudo-table is created during the operation. Defaults to a temporary table.

{: .important }
> It is highly recommended to use the [PostgreSqlBulkImportPseudoTableType.Temporary](/enumeration/postgresql/postgresqlbulkimportpseudotabletype#temporary) value in the `pseudoTableType` argument when working with parallelism.

## Usability

Pass the list of primary keys to the operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var primaryKeys = connection.Query<Person>(p => p.IsActive == false).Select(p => p.Id);
    var deletedRows = connection.BulkDeleteByKey("[dbo].[Person]",
        primaryKeys);
}
```

{: .note }
> It returns the number of rows deleted from the underlying table.

To specify a batch size:

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var primaryKeys = connection.Query<Person>(p => p.IsActive == false).Select(p => p.Id);
    var deletedRows = connection.BulkDeleteByKey("[dbo].[Person]",
        primaryKeys,
        batchSize: 100);
}
```

{: .important }
> If `batchSize` is not set, all items in the collection are sent at once.

## Physical Temporary Table

To use a physical pseudo-temporary table, pass [PostgreSqlBulkImportPseudoTableType.Temporary](/enumeration/postgresql/postgresqlbulkimportpseudotabletype#physical) in the `pseudoTableType` argument.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var primaryKeys = connection.Query<Person>(p => p.IsActive == false).Select(p => p.Id);
    var deletedRows = connection.BulkDeleteByKey("[dbo].[Person]",
        primaryKeys,
        pseudoTableType: PostgreSqlBulkImportPseudoTableType.Physical);
}
```

{: .note }
> A physical pseudo-temporary table improves performance over a standard temporary table, but is shared across all calls. Parallelism may fail in this scenario.

