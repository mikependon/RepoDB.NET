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

This enum is being used to define the type of pseudo-temporary table to be created during the bulk-import operations. This enumeration is only used for [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

It is used by the following bulk import operations.

- [BinaryBulkDelete](/operation/binarybulkdelete)
- [BinaryBulkDeleteByKey](/operation/binarybulkdeletebykey)
- [BinaryBulkInsert](/operation/binarybulkinsert)
- [BinaryBulkMerge](/operation/binarybulkmerge)
- [BinaryBulkUpdate](/operation/binarybulkupdate)

### Enum Values

- `Temporary` - a temporary pseudo-table will be created. The table is dedicated to the session of the connection and is automatically being destroyed once the connection is closed/disposed. Use this if you are working within an asynchronous environment.
- `Physical` - a physical pseudo-table will be created. The table is shared to any other connections. Use this if you prefer performance and is not working within an asynchronous environment.

### Usability

Simply pass value on the `pseudoTableType` argument.

Below is for [BinaryBulkDelete](/operation/binarybulkdelete) operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var deletedRows = connection.BinaryBulkDelete(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

Below is for [BinaryBulkDeleteByKey](/operation/binarybulkdeletebykey) operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var primaryKeys = GetPeople(1000).Select(e => e.Id);
    var deletedRows = connection.BinaryBulkDeleteByKey(primaryKeys,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

Below is for [BinaryBulkInsert](/operation/binarybulkinsert) operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BinaryBulkInsert(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

Below is for [BinaryBulkMerge](/operation/binarybulkmerge) operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BinaryBulkMerge(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

Below is for [BinaryBulkUpdate](/operation/binarybulkupdate) operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var updatedRows = connection.BinaryBulkUpdate(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

> By default, the `Temporary` is used and it is thread-safe in nature. The pseudo-temporary table that is being created is localized to the instance of the connection.