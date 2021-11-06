---
layout: default
sidebar: enumerations
title: "BulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the underlying target table."
permalink: /enumeration/postgresql/bulkimportidentitybehavior
tags: [repodb, class, bulkimportidentitybehavior, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: "PostgreSQL"
grand_parent: ENUMERATIONS
---

# BulkImportIdentityBehavior

---

An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the underlying target table. This enumeration is only used for [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

### Enum Values

- `KeepIdentity` - a value that indicates whether the value of the identity property/column will be kept and used.
- `ReturnIdentity` - a value that indicates whether the newly generated identity value from the target table will be set back to the entity.
- `Unspecified` - no action is required. (This is the default value)

### Usability

The enumeration is being used by both the [BinaryBulkInsert](/operation/binarybulkinsert) and [BinaryBulkMerge](/operation/binarybulkmerge) operations. Simply pass the value to the `identityBehavior` argument when calling the operation.

Let us say, you would like to insert a big dataset into the database by bulk and would like to return the generated identities afterwards, simply pass the value when calling the operation.

For [BinaryBulkInsert](/operation/binarybulkinsert) operation, see below.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BinaryBulkInsert(people,
        identityBehavior: BinaryImportIdentityBehavior.ReturnIdentity);
}
```

For [BinaryBulkMerge](/operation/binarybulkmerge) operation, see below.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BinaryBulkMerge(people,
        identityBehavior: BinaryImportIdentityBehavior.ReturnIdentity);
}
```