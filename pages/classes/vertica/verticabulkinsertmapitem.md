---
layout: default
sidebar: classes
title: "VerticaBulkInsertMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit VerticaType, for the Vertica bulk operations."
permalink: /class/vertica/verticabulkinsertmapitem
tags: [repodb, verticabulkinsertmapitem, vertica, bulk]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaBulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit `VerticaType` to bind with for the mapped column. It is not currently consumed by the `COPY`-stream-based bulk-copy implementation — Vertica's `COPY` parser infers each field's wire format from the destination column's actual server-side type — so it is kept only as a forward-looking escape hatch, matching the equivalent parameter on every other bulk-operations package's map-item type.

Used by the Vertica [BulkInsert](/operation/vertica/bulkinsert), [BulkMerge](/operation/vertica/bulkmerge) and [BulkUpdate](/operation/vertica/bulkupdate) operations.

## Create a new Instance

```csharp
var mapItem = new VerticaBulkInsertMapItem("SourceId", "DestinationId");
```

Or with an explicit `VerticaType`:

```csharp
var mapItem = new VerticaBulkInsertMapItem("SourceName", "DestinationName", VerticaType.VarChar);
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new VerticaBulkInsertMapItem("FirstName", "FName"),
    new VerticaBulkInsertMapItem("LastName", "LName")
};

using (var connection = new VerticaConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/vertica/bulkmerge) and [BulkUpdate](/operation/vertica/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
