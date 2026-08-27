---
layout: default
sidebar: enumerations
title: "FirebirdBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging (pseudo) table to be created during the bulk-import operations."
permalink: /enumeration/firebird/firebirdbulkimportpseudotabletype
tags: [repodb, firebirdbulkimportpseudotabletype]
parent: "Firebird"
grand_parent: ENUMERATIONS
---

# FirebirdBulkImportPseudoTableType

---

This enum defines the type of staging (pseudo) table created during bulk-import operations. It applies only to [Firebird](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations).

It is used by the following bulk operations, all part of [RepoDb.Firebird.BulkOperations](/operation/firebird).

- `BulkDelete`
- `BulkDeleteByKey`
- `BulkInsert` (only when `identityBehavior` is `ReturnIdentity`)
- `BulkMerge`
- `BulkUpdate`

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Chooses between `Physical` and `Memory` based on row count (`Physical` at 5,000 rows or more). This is the default. |
| Memory | Backs the operation with a Firebird `GLOBAL TEMPORARY TABLE ... ON COMMIT PRESERVE ROWS`. Rows are private to the connection that wrote them. |
| Physical | Backs the operation with an ordinary heap table. Faster to create for very large row counts than a global temporary table's per-connection storage, at the cost of the rows briefly existing as an ordinary (if uniquely-named) table. |

{: .note }
> Every pseudo table is created with a per-call unique name, so unlike some other providers' bulk-operations packages, `Physical` and `Memory` are both safe for concurrent callers writing against the same target table — there is no shared, deterministic staging-table name for them to race on.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new FbConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: FirebirdBulkImportIdentityBehavior.ReturnIdentity,
        pseudoTableType: FirebirdBulkImportPseudoTableType.Physical);
}
```
