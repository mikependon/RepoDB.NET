---
layout: default
sidebar: classes
title: "SapHanaCommandBatcherColumnMappingCollection"
description: "A collection used to define the source-to-destination column mappings of a SapHanaCommandBatcher."
permalink: /class/saphana/saphanacommandbatchercolumnmappingcollection
tags: [repodb, saphanacommandbatchercolumnmappingcollection, saphana, bulk]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaCommandBatcherColumnMappingCollection

---

This class is a `Collection<SapHanaBulkInsertMapItem>` used to define the source-to-destination column mappings of a [SapHanaCommandBatcher](/class/saphana/saphanacommandbatcher).

It backs [SapHanaCommandBatcher](/class/saphana/saphanacommandbatcher)'s `ColumnMappings` property, and reuses [SapHanaBulkInsertMapItem](/class/saphana/saphanabulkinsertmapitem) — the same mapping type `HanaBulkCopy` usage in this package binds against — rather than a duplicate type of its own. Its `Add(sourceColumn, destinationColumn)` overload creates and appends a [SapHanaBulkInsertMapItem](/class/saphana/saphanabulkinsertmapitem); an explicit `HanaDbType` for a mapping can instead be added directly via the base `Collection<T>.Add(SapHanaBulkInsertMapItem)`.

## Usability

```csharp
using var batcher = new SapHanaCommandBatcher(connection)
{
    DestinationTableName = "\"Person\"",
    TableName = "Person"
};

batcher.ColumnMappings.Add("FirstName", "FName");
batcher.ColumnMappings.Add("LastName", "LName");
batcher.ColumnMappings.Add(new SapHanaBulkInsertMapItem("Notes", "Notes", HanaDbType.NClob));
```

{: .note }
> When `ColumnMappings` is left empty, [SapHanaCommandBatcher](/class/saphana/saphanacommandbatcher) maps every source column to a destination column of the same name.
