---
layout: default
sidebar: enumerations
title: "Db2BulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/db2/db2bulkimportidentitybehavior
tags: [repodb, db2bulkimportidentitybehavior]
parent: "Db2"
grand_parent: ENUMERATIONS
---

# Db2BulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [Db2](https://www.nuget.org/packages/RepoDb.Db2.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The value of the identity property/column will be kept and used. (This is the default value) |
| ReturnIdentity | The newly generated identity value from the target table will be set back to the entity. |

{: .note }
> Unlike [OracleBulkImportIdentityBehavior](/enumeration/oracle/oraclebulkimportidentitybehavior), there is no `Unspecified` state — this enum defaults straight to `KeepIdentity`.

## Usability

This enum is used by both the [BulkInsert](/operation/db2/bulkinsert) and [BulkMerge](/operation/db2/bulkmerge) operations. Pass the value to the `identityBehavior` argument when calling the operation.

For [BulkInsert](/operation/db2/bulkinsert):

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: Db2BulkImportIdentityBehavior.ReturnIdentity);
}
```

For [BulkMerge](/operation/db2/bulkmerge):

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: Db2BulkImportIdentityBehavior.ReturnIdentity);
}
```
