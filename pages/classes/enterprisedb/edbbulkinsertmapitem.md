---
layout: default
sidebar: classes
title: "EDBBulkInsertMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit EDBType, for the EnterpriseDB bulk operations."
permalink: /class/enterprisedb/edbbulkinsertmapitem
tags: [repodb, edbbulkinsertmapitem, enterprisedb, bulk]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EDBBulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit `RepoDb.Connector.EnterpriseDb.EDBType` to bind with for the mapped column. When not provided, the type is inferred from the entity property's `[EDBType]` attribute (if present) or, failing that, from the .NET CLR value itself.

Used by the EnterpriseDB [BulkInsert](/operation/enterprisedb#bulkinsert), [BulkMerge](/operation/enterprisedb#bulkmerge) and [BulkUpdate](/operation/enterprisedb#bulkupdate) operations. Mappings are also type-checked: if a mapping's source and destination CLR types are incompatible (other than the allowed `Guid`↔`string` and integral-to-integral widenings), the write throws an `InvalidTypeException` up front rather than failing inside EnterpriseDB.

## Create a new Instance

```csharp
var mapItem = new EDBBulkInsertMapItem("SourceId", "DestinationId");
```

Or with an explicit `EDBType`:

```csharp
var mapItem = new EDBBulkInsertMapItem("SourceName", "DestinationName", EDBType.Varchar);
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new EDBBulkInsertMapItem("FirstName", "FName"),
    new EDBBulkInsertMapItem("LastName", "LName")
};

using (var connection = new EDBConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/enterprisedb#bulkmerge) and [BulkUpdate](/operation/enterprisedb#bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
