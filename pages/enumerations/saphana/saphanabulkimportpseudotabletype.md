---
layout: default
sidebar: enumerations
title: "SapHanaBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging (pseudo) table to be created during the bulk-import operations."
permalink: /enumeration/saphana/saphanabulkimportpseudotabletype
tags: [repodb, saphanabulkimportpseudotabletype]
parent: "SAP HANA"
grand_parent: ENUMERATIONS
---

# SapHanaBulkImportPseudoTableType

---

This enum defines the type of staging (pseudo) table created during bulk-import operations. It applies only to [SAP HANA](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations).

It is used by every operation in [RepoDb.SapHana.BulkOperations](/operation/saphana) — [BulkInsert](/operation/saphana/bulkinsert), [BulkMerge](/operation/saphana/bulkmerge), [BulkUpdate](/operation/saphana/bulkupdate), [BulkDelete](/operation/saphana/bulkdelete), and [BulkDeleteByKey](/operation/saphana/bulkdeletebykey).

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Intended to choose between `Physical` and `Memory` based on row count (`Physical` at 5,000 rows or more). This is the default. |
| Memory | Intended to back the operation with a HANA `LOCAL TEMPORARY` table. Rows would be session-private, isolating concurrent executions from different connections. |
| Physical | Backs the operation with an ordinary heap table, under a **deterministic name** derived from the table name, the operation, and this enum value (e.g. `PhysicalPersonMerge`) — not a per-call-unique name. Rows are not session-isolated, so concurrent callers targeting the same table with the same operation can see and interfere with each other's staged rows. |

{: .important }
> `Auto` and `Memory` both currently resolve to `Physical` at runtime — the internal resolution logic returns `Physical` on every outcome, so the `LOCAL TEMPORARY` (session-isolated) path advertised by `Memory` is not implemented yet despite the enum offering it. Combined with `Physical`'s deterministic (non-unique) pseudo-table name, this means **two concurrent bulk calls of the same operation against the same table currently always risk interfering with each other** — unlike [Db2BulkImportPseudoTableType](/enumeration/db2/db2bulkimportpseudotabletype) (also currently `Physical`-only, but that provider's own concurrency caveats are less severe) or [FirebirdBulkImportPseudoTableType](/enumeration/firebird/firebirdbulkimportpseudotabletype)/[VerticaBulkImportPseudoTableType](/enumeration/vertica/verticabulkimportpseudotabletype) (both of which use a per-call-unique name regardless of type). Avoid running concurrent SAP HANA bulk operations of the same kind against the same table until this is resolved.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: SapHanaBulkImportIdentityBehavior.ReturnIdentity,
        pseudoTableType: SapHanaBulkImportPseudoTableType.Physical);
}
```
