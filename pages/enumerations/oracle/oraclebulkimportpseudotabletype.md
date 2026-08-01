---
layout: default
sidebar: enumerations
title: "OracleBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging (pseudo) table to be created during the bulk-import operations."
permalink: /enumeration/oracle/oraclebulkimportpseudotabletype
tags: [repodb, oraclebulkimportpseudotabletype]
parent: "Oracle"
grand_parent: ENUMERATIONS
---

# OracleBulkImportPseudoTableType

---

This enum defines the type of staging (pseudo) table created during bulk-import operations. It applies only to [Oracle](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations).

It is used by the following bulk operations.

- [BulkDelete](/operation/sqlserver/bulkdelete)
- [BulkDeleteByKey](/operation/oracle/bulkdeletebykey)
- [BulkInsert](/operation/sqlserver/bulkinsert)
- [BulkMerge](/operation/sqlserver/bulkmerge)
- [BulkUpdate](/operation/sqlserver/bulkupdate)

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Chooses between `Physical` and `Memory` based on row count (`Physical` at 5,000 rows or more). This is the default. |
| Memory | A Global Temporary Table, private to each session. Safe for concurrent/multi-connection workloads. |
| Physical | An ordinary heap table, shared by every session. Only safe for sequential (non-concurrent) workloads against the same target table. |

{: .important }
> Every value currently resolves to `Physical` at runtime. ODP.NET's direct-path load — used internally by every bulk operation — cannot write into a Global Temporary Table, failing with `ORA-39826`. This is a temporary driver-level limitation; `Auto` and `Memory` are kept in the enum so existing code doesn't need to change once a working strategy is implemented. See [Operations (Oracle)](/operation/oracle#pseudo-table-type) for details.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        pseudoTableType: OracleBulkImportPseudoTableType.Physical);
}
```
