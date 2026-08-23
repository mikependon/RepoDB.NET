---
layout: default
sidebar: classes
title: "Db2BulkArrayBinderColumnMappingCollection"
description: "A collection used to define the source-to-destination column mappings of a Db2BulkArrayBinder."
permalink: /class/db2/db2bulkarraybindercolumnmappingcollection
tags: [repodb, db2bulkarraybindercolumnmappingcollection, db2, bulk]
parent: "Db2"
grand_parent: CLASSES
---

# Db2BulkArrayBinderColumnMappingCollection

---

This class is a `Collection<Db2BulkInsertMapItem>` used to define the source-to-destination column mappings of a [Db2BulkArrayBinder](/class/db2/db2bulkarraybinder), mirroring the shape of `DB2BulkCopyColumnMappingCollection`.

It backs [Db2BulkArrayBinder](/class/db2/db2bulkarraybinder)'s `ColumnMappings` property. Its `Add(sourceColumn, destinationColumn)` overload creates and appends a [Db2BulkInsertMapItem](/class/db2/db2bulkinsertmapitem); an explicit `DB2Type` for a mapping can instead be added directly via the base `Collection<T>.Add(Db2BulkInsertMapItem)`.

## Usability

```csharp
using var arrayBinder = new Db2BulkArrayBinder(connection)
{
    DestinationTableName = "Person"
};

arrayBinder.ColumnMappings.Add("FirstName", "FName");
arrayBinder.ColumnMappings.Add("LastName", "LName");
arrayBinder.ColumnMappings.Add(new Db2BulkInsertMapItem("Age", "Age", DB2Type.Integer));
```

{: .note }
> When `ColumnMappings` is left empty, [Db2BulkArrayBinder](/class/db2/db2bulkarraybinder) maps every source column to a destination column of the same name.
