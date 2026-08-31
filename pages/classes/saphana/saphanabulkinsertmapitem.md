---
layout: default
sidebar: classes
title: "SapHanaBulkInsertMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit HanaDbType, for the SAP HANA bulk operations."
permalink: /class/saphana/saphanabulkinsertmapitem
tags: [repodb, saphanabulkinsertmapitem, saphana, bulk]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaBulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit `HanaDbType` to bind with for the mapped column.

Used by the SAP HANA [BulkInsert](/operation/saphana/bulkinsert), [BulkMerge](/operation/saphana/bulkmerge) and [BulkUpdate](/operation/saphana/bulkupdate) operations. Mappings are also type-checked: if a mapping's source and destination CLR types are incompatible (other than the allowed `Guid`↔`string` and integral-to-integral widenings), the write throws an `InvalidTypeException` up front rather than failing inside SAP HANA.

## Create a new Instance

```csharp
var mapItem = new SapHanaBulkInsertMapItem("SourceId", "DestinationId");
```

Or with an explicit `HanaDbType`:

```csharp
var mapItem = new SapHanaBulkInsertMapItem("SourceName", "DestinationName", HanaDbType.NVarChar);
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new SapHanaBulkInsertMapItem("FirstName", "FName"),
    new SapHanaBulkInsertMapItem("LastName", "LName")
};

using (var connection = new HanaConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/saphana/bulkmerge) and [BulkUpdate](/operation/saphana/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
