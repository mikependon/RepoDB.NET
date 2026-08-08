---
layout: default
sidebar: enumerations
title: "MySqlConnectorBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging (pseudo) table to be created during the bulk-import operations."
permalink: /enumeration/mysqlconnector/mysqlconnectorbulkimportpseudotabletype
tags: [repodb, mysqlconnectorbulkimportpseudotabletype]
parent: "MySqlConnector"
grand_parent: ENUMERATIONS
---

# MySqlConnectorBulkImportPseudoTableType

---

This enum defines the type of staging (pseudo) table created during bulk-import operations. It applies only to [MySqlConnector](https://www.nuget.org/packages/RepoDb.MySqlConnector.BulkOperations).

It is used by the following bulk operations.

- [BulkDelete](/operation/mysqlconnector/bulkdelete)
- [BulkDeleteByKey](/operation/mysqlconnector/bulkdeletebykey)
- [BulkInsert](/operation/mysqlconnector/bulkinsert)
- [BulkMerge](/operation/mysqlconnector/bulkmerge)
- [BulkUpdate](/operation/mysqlconnector/bulkupdate)

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Chooses between `Physical` and `Memory` based on row count (`Physical` at 5,000 rows or more). This is the default. |
| Memory | A `TEMPORARY` table, private to each session. Safe for concurrent/multi-connection workloads. |
| Physical | An ordinary heap table, shared by every session. Only safe for sequential (non-concurrent) workloads against the same target table. |

{: .important }
> Every value currently resolves to `Physical` at runtime, regardless of the value passed or the row count. `Auto` and `Memory` are kept in the enum so existing code doesn't need to change once the session-isolated `TEMPORARY` table path is fully wired up. See [Operations (MySqlConnector)](/operation/mysqlconnector#pseudo-table-type) for details.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        pseudoTableType: MySqlConnectorBulkImportPseudoTableType.Physical);
}
```
