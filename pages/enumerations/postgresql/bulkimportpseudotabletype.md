---
layout: default
sidebar: enumerations
title: "BulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of pseudo-temporary table to be created during the bulk-import operations."
permalink: /enumeration/postgresql/bulkimportpseudotabletype
tags: [repodb, class, bulkimportpseudotabletype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "PostgreSQL"
grand_parent: ENUMERATIONS
---

# BulkImportPseudoTableType

---

An enumeration that is being used to define the type of pseudo-temporary table to be created during the bulk-import operations. This enumeration is only used for [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

This enumeration is being used by the following bulk import operations.

- [BinaryBulkDelete](/operation/binarybulkdelete)
- [BinaryBulkDeleteByKey](/operation/binarybulkdeletebykey)
- [BinaryBulkInsert](/operation/binarybulkinsert)
- [BinaryBulkMerge](/operation/binarybulkmerge)
- [BinaryBulkUpdate](/operation/binarybulkupdate)

### Enum Values

- `Temporary` - a temporary pseudo-table will be created. The table is dedicated to the session of the connection and is automatically being destroyed once the connection is closed/disposed. Use this if you are working within an asynchronous environment.
- `Physical` - a physical pseudo-table will be created. The table is shared to any other connections. Use this if you prefer performance and is not working within an asynchronous environment.

### Usability

Let us say, you would like to merge a big dataset into the database that uses the physical pseudo-temporary table, then simply pass `Physical` value on the `pseudoTableType` argument. See below.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BinaryBulkInsert(people,
        pseudoTableType: BulkImportPseudoTableType.Physical);
}
```

> By default, the `Temporary` is used and it is thread-safe in nature. The pseudo-temporary table that is being created is localized to the instance of the connection.