---
layout: default
sidebar: enumerations
title: "SapHanaBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/saphana/saphanabulkimportidentitybehavior
tags: [repodb, saphanabulkimportidentitybehavior]
parent: "SAP HANA"
grand_parent: ENUMERATIONS
---

# SapHanaBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [SAP HANA](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The value of the identity property/column will be kept and used. (This is the default value) |
| ReturnIdentity | The newly generated identity value from the target table will be set back to the entity. |

{: .note }
> There is no `Unspecified` state — this enum defaults straight to `KeepIdentity`, the same shape as [Db2BulkImportIdentityBehavior](/enumeration/db2/db2bulkimportidentitybehavior), [FirebirdBulkImportIdentityBehavior](/enumeration/firebird/firebirdbulkimportidentitybehavior), and [VerticaBulkImportIdentityBehavior](/enumeration/vertica/verticabulkimportidentitybehavior).

{: .important }
> `ReturnIdentity`'s identity computation — a client-computed "next identity = `MAX(identity) + 1`" seeded value, combined with row-order arithmetic for [BulkInsert](/operation/saphana/bulkinsert), and a three-step matched/unmatched assignment for [BulkMerge](/operation/saphana/bulkmerge) — is the least-verified part of the whole provider (per the source's own remarks). Verify it carefully, especially under concurrent writers to the same table, before relying on it in production.

## Usability

This enum is used by both the [BulkInsert](/operation/saphana/bulkinsert) and [BulkMerge](/operation/saphana/bulkmerge) operations of [RepoDb.SapHana.BulkOperations](/operation/saphana). Pass the value to the `identityBehavior` argument when calling the operation.

For [BulkInsert](/operation/saphana/bulkinsert):

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: SapHanaBulkImportIdentityBehavior.ReturnIdentity);
}
```

For [BulkMerge](/operation/saphana/bulkmerge):

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: SapHanaBulkImportIdentityBehavior.ReturnIdentity);
}
```
