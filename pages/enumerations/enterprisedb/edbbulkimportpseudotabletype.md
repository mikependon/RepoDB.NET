---
layout: default
sidebar: enumerations
title: "EDBBulkImportPseudoTableType"
description: "An enumeration that is being used to define the type of staging table to be created during the bulk-import operations."
permalink: /enumeration/enterprisedb/edbbulkimportpseudotabletype
tags: [repodb, edbbulkimportpseudotabletype]
parent: "EnterpriseDB"
grand_parent: ENUMERATIONS
---

# EDBBulkImportPseudoTableType

---

This enum defines the kind of staging table backing a `BulkInsert` (when returning identities), `BulkMerge`, `BulkUpdate`, `BulkDelete`, or `BulkDeleteByKey` operation against EnterpriseDB. It applies only to [EnterpriseDB](https://www.nuget.org/packages/RepoDb.EnterpriseDb.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| Auto | Selects `Physical` when the row count being bulk-written is at least 5,000, otherwise selects `Memory`. This is the default. |
| Memory | Backs the operation with a `TEMP` table. Rows are session-private, isolating the execution from concurrent executions on different connections. |
| Physical | Backs the operation with an ordinary heap table. Rows are not session-isolated, so concurrent callers targeting the same table with the same operation can see and interfere with each other's staged rows, but writes are faster since no temporary-table machinery is involved. |

{: .important }
> The staging table name is **deterministic**, not per-call-unique — built from the pseudo table type, the target table name, and the operation (e.g. `PhysicalPersonMerge`). Two concurrent bulk calls of the *same operation* against the *same table*, both resolving to the same `pseudoTableType`, can interfere with each other's staged rows. `Memory` avoids this by scoping the table to the session (each connection gets its own), so prefer it when running concurrent bulk operations against the same table from different connections.

## Usability

Pass the value to the `pseudoTableType` argument of the target operation.

```csharp
using (var connection = new EDBConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: EDBBulkImportIdentityBehavior.ReturnIdentity,
        pseudoTableType: EDBBulkImportPseudoTableType.Memory);
}
```
