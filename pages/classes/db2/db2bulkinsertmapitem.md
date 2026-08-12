---
layout: default
sidebar: classes
title: "Db2BulkInsertMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit DB2Type, for the Db2 bulk operations."
permalink: /class/db2/db2bulkinsertmapitem
tags: [repodb, db2bulkinsertmapitem, db2, bulk]
parent: "Db2"
grand_parent: CLASSES
---

# Db2BulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit `DB2Type` to bind with for the mapped column. When not provided, the type is inferred from the entity property's `[DB2Type]`/`[Db2DbTypeEx]` attribute (if present) or, failing that, from the .NET CLR value itself.

Used by the Db2 [BulkInsert](/operation/db2/bulkinsert), [BulkMerge](/operation/db2/bulkmerge) and [BulkUpdate](/operation/db2/bulkupdate) operations.

## Create a new Instance

```csharp
var mapItem = new Db2BulkInsertMapItem("SourceId", "DestinationId");
```

Or with an explicit `DB2Type`:

```csharp
var mapItem = new Db2BulkInsertMapItem("SourceName", "DestinationName", DB2Type.VarChar);
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new Db2BulkInsertMapItem("FirstName", "FName"),
    new Db2BulkInsertMapItem("LastName", "LName"),
    new Db2BulkInsertMapItem("Age", "Age", DB2Type.Integer)
};

using (var connection = new DB2Connection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/db2/bulkmerge) and [BulkUpdate](/operation/db2/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
