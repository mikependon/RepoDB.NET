---
layout: default
sidebar: enumerations
title: "OracleBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/oracle/oraclebulkimportidentitybehavior
tags: [repodb, oraclebulkimportidentitybehavior]
parent: "Oracle"
grand_parent: ENUMERATIONS
---

# OracleBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [Oracle](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| Unspecified | No action is required. (This is the default value) |
| KeepIdentity | The value of the identity property/column will be kept and used. |
| ReturnIdentity | The newly generated identity value from the target table will be set back to the entity. |

## Usability

This enum is used by both the [BulkInsert](/operation/bulkinsert) and [BulkMerge](/operation/bulkmerge) operations. Pass the value to the `identityBehavior` argument when calling the operation.

For [BulkInsert](/operation/bulkinsert):

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: OracleBulkImportIdentityBehavior.ReturnIdentity);
}
```

For [BulkMerge](/operation/bulkmerge):

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: OracleBulkImportIdentityBehavior.ReturnIdentity);
}
```
