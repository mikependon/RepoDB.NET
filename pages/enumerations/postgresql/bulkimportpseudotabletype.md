---
layout: default
sidebar: enumerations
title: "BulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of pseudo-temporary table to be created during the bulk-import operations."
permalink: /enumeration/postgresql/bulkimportpseudotabletype
tags: [repodb, bulkimportpseudotabletype]
parent: "PostgreSQL"
grand_parent: ENUMERATIONS
---

# BulkImportPseudoTableType

---

This enum defines the type of pseudo-temporary table created during bulk-import operations. It applies only to the [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

It is used by the following bulk import operations.

- [BinaryBulkDelete](/operation/binarybulkdelete)
- [BinaryBulkDeleteByKey](/operation/binarybulkdeletebykey)
- [BinaryBulkInsert](/operation/binarybulkinsert)
- [BinaryBulkMerge](/operation/binarybulkmerge)
- [BinaryBulkUpdate](/operation/binarybulkupdate)

## Enum Values

| Name | Description |
|:-----|:------------|
| Temporary | A temporary pseudo-table will be created. The table is dedicated to the session of the connection and is automatically being destroyed once the connection is closed/disposed. Use this if you are working within an asynchronous environment.
| Physical | A physical pseudo-table will be created. The table is shared to any other connections. Use this if you prefer performance and is not working within an asynchronous environment. |

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

For [BinaryBulkDelete](/operation/binarybulkdelete):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var deletedRows = connection.BinaryBulkDelete(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

For [BinaryBulkDeleteByKey](/operation/binarybulkdeletebykey):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var primaryKeys = GetPeople(1000).Select(e => e.Id);
    var deletedRows = connection.BinaryBulkDeleteByKey(primaryKeys,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

For [BinaryBulkInsert](/operation/binarybulkinsert):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BinaryBulkInsert(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

For [BinaryBulkMerge](/operation/binarybulkmerge):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BinaryBulkMerge(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

For [BinaryBulkUpdate](/operation/binarybulkupdate):

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var updatedRows = connection.BinaryBulkUpdate(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

{: .important }
> By default, the `Temporary` is used and it is thread-safe in nature. The pseudo-temporary table that is being created is localized to the instance of the connection.
