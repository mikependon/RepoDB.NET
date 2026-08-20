---
layout: default
sidebar: classes
title: "ClickHouseBulkInsertMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit ClickHouse type name, for the ClickHouse bulk operations."
permalink: /class/clickhouse/clickhousebulkinsertmapitem
tags: [repodb, clickhousebulkinsertmapitem, clickhouse, bulk]
parent: "ClickHouse"
grand_parent: CLASSES
---

# ClickHouseBulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit ClickHouse type name for the mapped column. When omitted, the type falls back to the entity property's [ClickHouseType](/attribute/clickhouse/clickhousetype) attribute, or is otherwise inferred from the .NET CLR type.

Used by the ClickHouse [BulkInsert](/operation/clickhouse/bulkinsert), [BulkMerge](/operation/clickhouse/bulkmerge) and [BulkUpdate](/operation/clickhouse/bulkupdate) operations.

## Create a new Instance

```csharp
var mapItem = new ClickHouseBulkInsertMapItem("SourceId", "DestinationId");
```

Or with an explicit ClickHouse type name:

```csharp
var mapItem = new ClickHouseBulkInsertMapItem("SourceCreatedDateUtc", "DestinationCreatedDateUtc", "Nullable(DateTime64(3))");
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new ClickHouseBulkInsertMapItem("FirstName", "FName"),
    new ClickHouseBulkInsertMapItem("LastName", "LName"),
    new ClickHouseBulkInsertMapItem("CreatedDateUtc", "CreatedDateUtc", "Nullable(DateTime64(3))")
};

using (var connection = new ClickHouseConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/clickhouse/bulkmerge) and [BulkUpdate](/operation/clickhouse/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name.
