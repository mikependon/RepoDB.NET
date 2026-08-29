---
layout: default
sidebar: classes
title: "FirebirdCommandBatcherMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit FbDbType, for the Firebird bulk operations."
permalink: /class/firebird/firebirdcommandbatchermapitem
tags: [repodb, firebirdcommandbatchermapitem, firebird, bulk]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdCommandBatcherMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit `FbDbType` to bind with for the mapped column. When not provided, the bind parameter's wire format is determined by Firebird's DSQL layer from the destination column's own server-side type, not the client-declared type — so this is only an escape hatch for genuinely ambiguous cases (e.g. forcing a specific `BLOB` sub-type).

Used by the Firebird [BulkInsert](/operation/firebird/bulkinsert), [BulkMerge](/operation/firebird/bulkmerge) and [BulkUpdate](/operation/firebird/bulkupdate) operations.

## Create a new Instance

```csharp
var mapItem = new FirebirdCommandBatcherMapItem("SourceId", "DestinationId");
```

Or with an explicit `FbDbType`:

```csharp
var mapItem = new FirebirdCommandBatcherMapItem("SourceBlob", "DestinationBlob", FbDbType.Binary);
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new FirebirdCommandBatcherMapItem("FirstName", "FName"),
    new FirebirdCommandBatcherMapItem("LastName", "LName"),
    new FirebirdCommandBatcherMapItem("Photo", "Photo", FbDbType.Binary)
};

using (var connection = new FbConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/firebird/bulkmerge) and [BulkUpdate](/operation/firebird/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
