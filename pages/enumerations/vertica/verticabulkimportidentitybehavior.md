---
layout: default
sidebar: enumerations
title: "VerticaBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/vertica/verticabulkimportidentitybehavior
tags: [repodb, verticabulkimportidentitybehavior]
parent: "Vertica"
grand_parent: ENUMERATIONS
---

# VerticaBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [Vertica](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The value of the identity property/column will be kept and used. (This is the default value) |
| ReturnIdentity | The newly generated identity value from the target table will be set back to the entity. |

{: .note }
> There is no `Unspecified` state — this enum defaults straight to `KeepIdentity`, the same shape as [Db2BulkImportIdentityBehavior](/enumeration/db2/db2bulkimportidentitybehavior) and [FirebirdBulkImportIdentityBehavior](/enumeration/firebird/firebirdbulkimportidentitybehavior).

## Usability

This enum is used by both the [BulkInsert](/operation/vertica/bulkinsert) and [BulkMerge](/operation/vertica/bulkmerge) operations of [RepoDb.Vertica.BulkOperations](/operation/vertica). Pass the value to the `identityBehavior` argument when calling the operation.

For [BulkInsert](/operation/vertica/bulkinsert):

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: VerticaBulkImportIdentityBehavior.ReturnIdentity);
}
```

For [BulkMerge](/operation/vertica/bulkmerge):

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: VerticaBulkImportIdentityBehavior.ReturnIdentity);
}
```
