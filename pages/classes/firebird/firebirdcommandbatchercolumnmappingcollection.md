---
layout: default
sidebar: classes
title: "FirebirdCommandBatcherColumnMappingCollection"
description: "A collection used to define the source-to-destination column mappings of a FirebirdCommandBatcher."
permalink: /class/firebird/firebirdcommandbatchercolumnmappingcollection
tags: [repodb, firebirdcommandbatchercolumnmappingcollection, firebird, bulk]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdCommandBatcherColumnMappingCollection

---

This class is a `Collection<FirebirdCommandBatcherMapItem>` used to define the source-to-destination column mappings of a [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher).

It backs [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher)'s `ColumnMappings` property. Its `Add(sourceColumn, destinationColumn)` overload creates and appends a [FirebirdCommandBatcherMapItem](/class/firebird/firebirdcommandbatchermapitem); an explicit `FbDbType` for a mapping can instead be added directly via the base `Collection<T>.Add(FirebirdCommandBatcherMapItem)`.

## Usability

```csharp
using var batcher = new FirebirdCommandBatcher(connection)
{
    DestinationTableName = "\"Person\""
};

batcher.ColumnMappings.Add("FirstName", "FName");
batcher.ColumnMappings.Add("LastName", "LName");
batcher.ColumnMappings.Add(new FirebirdCommandBatcherMapItem("Photo", "Photo", FbDbType.Binary));
```

{: .note }
> When `ColumnMappings` is left empty, [FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher) maps every source column to a destination column of the same name.
