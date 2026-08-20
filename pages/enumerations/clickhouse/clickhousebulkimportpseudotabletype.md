---
layout: default
sidebar: enumerations
title: "ClickHouseBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging (pseudo) table to be created during the bulk-import operations."
permalink: /enumeration/clickhouse/clickhousebulkimportpseudotabletype
tags: [repodb, clickhousebulkimportpseudotabletype, clickhouse]
parent: "ClickHouse"
grand_parent: ENUMERATIONS
---

# ClickHouseBulkImportPseudoTableType

---

This enum defines the type of staging (pseudo) table created during bulk-import operations. It applies to [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations).

It is used by the following bulk operations.

- [BulkDelete](/operation/clickhouse/bulkdelete)
- [BulkDeleteByKey](/operation/clickhouse/bulkdeletebykey)
- [BulkMerge](/operation/clickhouse/bulkmerge)
- [BulkUpdate](/operation/clickhouse/bulkupdate)

{: .note }
> [BulkInsert](/operation/clickhouse/bulkinsert) never uses a pseudo table — this enum has no effect on it. Rows are always written straight to the target table, since `identityBehavior: ReturnIdentity` (the only reason a pseudo table would be needed for an insert) is never supported for ClickHouse.

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Chooses between `Physical` and `Memory` based on row count (`Physical` at 5,000 rows or more). This is the default. |
| Memory | A staging table backed by ClickHouse's `Memory` table engine, private to the current session. |
| Physical | A staging table backed by an ordinary `MergeTree` table (`ORDER BY tuple()`). Not session-isolated, but faster to create. |

{: .important }
> Every value currently resolves to `Physical` at runtime — the internal resolution method returns `Physical` on both branches of its row-count check, regardless of the value passed in or the actual row count. The `Memory` branch is fully implemented in the SQL builder but not yet reachable. Because a physical pseudo table has no per-session isolation, avoid running concurrent bulk operations against the same target table until this is resolved. See [Operations (ClickHouse)](/operation/clickhouse#pseudo-table-type) for details.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        pseudoTableType: ClickHouseBulkImportPseudoTableType.Physical);
}
```
