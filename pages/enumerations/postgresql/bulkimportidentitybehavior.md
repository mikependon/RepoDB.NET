---
layout: default
sidebar: enumerations
title: "BulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/postgresql/bulkimportidentitybehavior
tags: [repodb, bulkimportidentitybehavior]
parent: "PostgreSQL"
grand_parent: ENUMERATIONS
---

# BulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to the [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | A value that indicates whether the value of the identity property/column will be kept and used. |
| ReturnIdentity | A value that indicates whether the newly generated identity value from the target table will be set back to the entity. |
| Unspecified | No action is required. (This is the default value) |

## Usability

This enum is used by both the [BinaryBulkInsert](/operation/binarybulkinsert) and [BinaryBulkMerge](/operation/binarybulkmerge) operations. Pass the value to the `identityBehavior` argument when calling the operation.

For the [BinaryBulkInsert](/operation/binarybulkinsert) operation:

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BinaryBulkInsert(people,
        identityBehavior: BinaryImportIdentityBehavior.ReturnIdentity);
}
```

For the [BinaryBulkMerge](/operation/binarybulkmerge) operation:

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BinaryBulkMerge(people,
        identityBehavior: BinaryImportIdentityBehavior.ReturnIdentity);
}
```
