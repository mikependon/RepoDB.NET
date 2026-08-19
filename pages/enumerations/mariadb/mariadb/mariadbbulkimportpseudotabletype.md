---
layout: default
sidebar: enumerations
title: "MariaDbBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging (pseudo) table to be created during the bulk-import operations."
permalink: /enumeration/mariadb/mariadb/mariadbbulkimportpseudotabletype
tags: [repodb, mariadbbulkimportpseudotabletype, mariadb]
parent: "MariaDb"
grand_parent: ENUMERATIONS
---

# MariaDbBulkImportPseudoTableType

---

This enum defines the type of staging (pseudo) table created during bulk-import operations. It applies only to [RepoDb.MariaDb.BulkOperations](https://www.nuget.org/packages/RepoDb.MariaDb.BulkOperations) (the `MySql.Data`-based driver).

It is used by the following bulk operations.

- [BulkDelete](/operation/mariadb/bulkdelete)
- [BulkDeleteByKey](/operation/mariadb/bulkdeletebykey)
- [BulkInsert](/operation/mariadb/bulkinsert)
- [BulkMerge](/operation/mariadb/bulkmerge)
- [BulkUpdate](/operation/mariadb/bulkupdate)

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Chooses between `Physical` and `Memory` based on row count (`Physical` at 5,000 rows or more). This is the default. |
| Memory | A `TEMPORARY` table, private to each session. Safe for concurrent/multi-connection workloads. |
| Physical | An ordinary heap table, shared by every session. Only safe for sequential (non-concurrent) workloads against the same target table. |

{: .important }
> Every value currently resolves to `Physical` at runtime, regardless of the value passed or the row count. `Auto` and `Memory` are kept in the enum so existing code doesn't need to change once the session-isolated `TEMPORARY` table path is fully wired up. See [Operations (MariaDb)](/operation/mariadb#pseudo-table-type) for details.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        pseudoTableType: MariaDbBulkImportPseudoTableType.Physical);
}
```
