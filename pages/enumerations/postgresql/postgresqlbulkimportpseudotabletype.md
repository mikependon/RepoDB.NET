---
layout: default
sidebar: enumerations
title: "PostgreSqlBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of pseudo-temporary table to be created during the bulk-import operations."
permalink: /enumeration/postgresql/postgresqlbulkimportpseudotabletype
tags: [repodb, postgresqlbulkimportpseudotabletype]
parent: "PostgreSQL"
grand_parent: ENUMERATIONS
---

# PostgreSqlBulkImportPseudoTableType

---

{: .warning }
> This enum was previously named `BulkImportPseudoTableType`. That name is now deprecated in favor of `PostgreSqlBulkImportPseudoTableType` and will be tagged starting v1.16.0 of the `RepoDb.PostgreSql` and `RepoDb.PostgreSql.BulkOperations` packages.

This enum defines the type of pseudo-temporary table created during bulk-import operations. It applies only to the [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

It is used by the following bulk import operations.

- [BulkDelete](/operation/postgresql/bulkdelete)
- [BulkDeleteByKey](/operation/postgresql/bulkdeletebykey)
- [BulkInsert](/operation/postgresql/bulkinsert)
- [BulkMerge](/operation/postgresql/bulkmerge)
- [BulkUpdate](/operation/postgresql/bulkupdate)

## Enum Values

| Name | Description |
|:-----|:------------|
| Temporary | A temporary pseudo-table will be created. The table is dedicated to the session of the connection and is automatically being destroyed once the connection is closed/disposed. Use this if you are working within an asynchronous environment.
| Physical | A physical pseudo-table will be created. The table is shared to any other connections. Use this if you prefer performance and is not working within an asynchronous environment. |

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

For [BulkDelete](/operation/postgresql/bulkdelete):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var deletedRows = connection.BulkDelete(people,
        pseudoTableType: PostgreSqlBulkImportPseudoTableType.Physical);
}
```

For [BulkDeleteByKey](/operation/postgresql/bulkdeletebykey):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var primaryKeys = GetPeople(1000).Select(e => e.Id);
    var deletedRows = connection.BulkDeleteByKey(primaryKeys,
        pseudoTableType: PostgreSqlBulkImportPseudoTableType.Physical);
}
```

For [BulkInsert](/operation/postgresql/bulkinsert):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        pseudoTableType: PostgreSqlBulkImportPseudoTableType.Physical);
}
```

For [BulkMerge](/operation/postgresql/bulkmerge):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        pseudoTableType: PostgreSqlBulkImportPseudoTableType.Physical);
}
```

For [BulkUpdate](/operation/postgresql/bulkupdate):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var updatedRows = connection.BulkUpdate(people,
        pseudoTableType: PostgreSqlBulkImportPseudoTableType.Physical);
}
```

{: .important }
> By default, the `Temporary` is used and it is thread-safe in nature. The pseudo-temporary table that is being created is localized to the instance of the connection.
