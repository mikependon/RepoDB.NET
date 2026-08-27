---
layout: default
sidebar: enumerations
title: "FirebirdBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/firebird/firebirdbulkimportidentitybehavior
tags: [repodb, firebirdbulkimportidentitybehavior]
parent: "Firebird"
grand_parent: ENUMERATIONS
---

# FirebirdBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [Firebird](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The value of the identity property/column will be kept and used. (This is the default value) |
| ReturnIdentity | The newly generated identity value from the target table will be set back to the entity. |

{: .note }
> There is no `Unspecified` state — this enum defaults straight to `KeepIdentity`, the same shape as [Db2BulkImportIdentityBehavior](/enumeration/db2/db2bulkimportidentitybehavior).

## Usability

This enum is used by both the `BulkInsert` and `BulkMerge` operations of [RepoDb.Firebird.BulkOperations](/operation/firebird). Pass the value to the `identityBehavior` argument when calling the operation.

For `BulkInsert`:

```csharp
using (var connection = new FbConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: FirebirdBulkImportIdentityBehavior.ReturnIdentity);
}
```

For `BulkMerge`:

```csharp
using (var connection = new FbConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: FirebirdBulkImportIdentityBehavior.ReturnIdentity);
}
```
