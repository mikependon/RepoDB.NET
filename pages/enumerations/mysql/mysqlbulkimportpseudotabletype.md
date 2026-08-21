---
layout: default
sidebar: enumerations
title: "MySqlBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging (pseudo) table to be created during the bulk-import operations."
permalink: /enumeration/mysql/mysqlbulkimportpseudotabletype
tags: [repodb, mysqlbulkimportpseudotabletype, mysql]
parent: "MySql"
grand_parent: ENUMERATIONS
---

# MySqlBulkImportPseudoTableType

---

This enum defines the type of staging (pseudo) table created during bulk-import operations. It applies only to [RepoDb.MySql.BulkOperations](https://www.nuget.org/packages/RepoDb.MySql.BulkOperations) (the `MySql.Data`-based driver).

It is used by the following bulk operations.

- [BulkDelete](/operation/mysql/bulkdelete)
- [BulkDeleteByKey](/operation/mysql/bulkdeletebykey)
- [BulkInsert](/operation/mysql/bulkinsert)
- [BulkMerge](/operation/mysql/bulkmerge)
- [BulkUpdate](/operation/mysql/bulkupdate)

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Chooses between `Physical` and `Memory` based on row count. This is the default. |
| Memory | A `TEMPORARY` table, private to each session. Safe for concurrent/multi-connection workloads. |
| Physical | An ordinary heap table, shared by every session. Only safe for sequential (non-concurrent) workloads against the same target table. |

{: .important }
> Every value currently resolves to `Physical` at runtime, including an explicit `Memory` and `Auto`'s row-count threshold. The `TEMPORARY TABLE` branch is fully implemented in the SQL builder, but the resolution step that picks between them (`ResolvePseudoTableType`) currently maps every input to `Physical` unconditionally, until that path is enabled and verified against a live server. See [Operations (MySql)](/operation/mysql#pseudo-table-type) for details.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        pseudoTableType: MySqlBulkImportPseudoTableType.Physical);
}
```
