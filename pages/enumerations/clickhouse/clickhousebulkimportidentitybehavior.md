---
layout: default
sidebar: enumerations
title: "ClickHouseBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/clickhouse/clickhousebulkimportidentitybehavior
tags: [repodb, clickhousebulkimportidentitybehavior, clickhouse]
parent: "ClickHouse"
grand_parent: ENUMERATIONS
---

# ClickHouseBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies to [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The identity/key value is written to the target table exactly as supplied on the entity. This is the only supported behavior, and the default. |
| ReturnIdentity | Not supported. ClickHouse has no session-wide scope identity, sequence, or auto-increment mechanism, so requesting this value throws a `NotSupportedException`. |

## Usability

This enum is used by both the [BulkInsert](/operation/clickhouse/bulkinsert) and [BulkMerge](/operation/clickhouse/bulkmerge) operations. Since `KeepIdentity` is the default, most callers never need to pass it explicitly.

```csharp
using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: ClickHouseBulkImportIdentityBehavior.KeepIdentity);
}
```

{: .important }
> Passing `identityBehavior: ClickHouseBulkImportIdentityBehavior.ReturnIdentity` to [BulkInsert](/operation/clickhouse/bulkinsert) or [BulkMerge](/operation/clickhouse/bulkmerge) always throws `NotSupportedException` — ClickHouse has no mechanism to generate or return identity values. Assign key values on the entity yourself before calling either operation.
