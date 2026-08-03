---
layout: default
sidebar: classes
title: "PostgreSqlBulkInsertMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit NpgsqlDbType or data type name, for the PostgreSQL bulk operations."
permalink: /class/postgresql/postgresqlbulkinsertmapitem
tags: [repodb, postgresqlbulkinsertmapitem, postgresql, bulk]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlBulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit `NpgsqlDbType` or PostgreSQL data type name to bind with for the mapped column. When neither is provided, the type is resolved from the .NET CLR type via the [ClientTypeToNpgsqlDbTypeResolver](/class/postgresql/clienttypetonpgsqldbtyperesolver).

Used by the PostgreSQL [BulkInsert](/operation/postgresql/bulkinsert), [BulkMerge](/operation/postgresql/bulkmerge) and [BulkUpdate](/operation/postgresql/bulkupdate) operations.

{: .note }
> This class replaces `NpgsqlBulkInsertMapItem`, which is now deprecated. Existing code using `NpgsqlBulkInsertMapItem` continues to work, as it is now a subclass of `PostgreSqlBulkInsertMapItem` kept only for backward compatibility.

## Create a new Instance

```csharp
var mapItem = new PostgreSqlBulkInsertMapItem("SourceId", "DestinationId");
```

Or with an explicit .NET CLR `Type` (resolved to an `NpgsqlDbType`):

```csharp
var mapItem = new PostgreSqlBulkInsertMapItem("SourceAge", "DestinationAge", typeof(int));
```

Or with an explicit `NpgsqlDbType`:

```csharp
var mapItem = new PostgreSqlBulkInsertMapItem("SourceName", "DestinationName", NpgsqlDbType.Varchar);
```

Or with an explicit PostgreSQL data type name (e.g. a native enum type), which takes precedence over `NpgsqlDbType` when both are relevant:

```csharp
var mapItem = new PostgreSqlBulkInsertMapItem("SourceHand", "DestinationHand", "hand");
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new PostgreSqlBulkInsertMapItem("FirstName", "FName"),
    new PostgreSqlBulkInsertMapItem("LastName", "LName"),
    new PostgreSqlBulkInsertMapItem("Age", "Age", NpgsqlDbType.Integer),
    new PostgreSqlBulkInsertMapItem("Hand", "Hand", "hand")
};

using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/postgresql/bulkmerge) and [BulkUpdate](/operation/postgresql/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
