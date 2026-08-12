---
layout: default
sidebar: enumerations
title: "Db2BulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging (pseudo) table to be created during the bulk-import operations."
permalink: /enumeration/db2/db2bulkimportpseudotabletype
tags: [repodb, db2bulkimportpseudotabletype]
parent: "Db2"
grand_parent: ENUMERATIONS
---

# Db2BulkImportPseudoTableType

---

This enum defines the type of staging (pseudo) table created during bulk-import operations. It applies only to [Db2](https://www.nuget.org/packages/RepoDb.Db2.BulkOperations).

It is used by the following bulk operations.

- [BulkDelete](/operation/db2/bulkdelete)
- [BulkDeleteByKey](/operation/db2/bulkdeletebykey)
- [BulkInsert](/operation/db2/bulkinsert)
- [BulkMerge](/operation/db2/bulkmerge)
- [BulkUpdate](/operation/db2/bulkupdate)

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Chooses between `Physical` and `Memory` based on row count (`Physical` at 5,000 rows or more). This is the default. |
| Memory | A session-private temporary table. Safe for concurrent/multi-connection workloads. |
| Physical | An ordinary heap table, shared by every session. Only safe for sequential (non-concurrent) workloads against the same target table. |

{: .important }
> `Auto` and `Memory` both currently resolve to `Physical` at runtime — the internal auto-resolution logic returns `Physical` on every outcome, so there is no session-private staging path implemented yet despite the enum advertising one. `Auto` and `Memory` are kept in the enum so existing code doesn't need to change once a working strategy is implemented. See [Operations (Db2)](/operation/db2#pseudo-table-type) for details.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        pseudoTableType: Db2BulkImportPseudoTableType.Physical);
}
```
