---
layout: default
sidebar: classes
title: "MySqlConnectorBulkInsertMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit MySqlDbType, for the MySqlConnector bulk operations."
permalink: /class/mysql/mysqlconnector/mysqlconnectorbulkinsertmapitem
tags: [repodb, mysqlconnectorbulkinsertmapitem, mysql, mysqlconnector, bulk]
parent: "MySQL"
grand_parent: CLASSES
nav_order: 15
---

# MySqlConnectorBulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit `MySqlDbType` to bind with for the mapped column. When not provided, the type is inferred from the entity property's `[MySqlDbType]`/`[MySqlConnectorDbTypeEx]` attribute (if present) or, failing that, from the .NET CLR value itself.

Used by the [BulkInsert](/operation/mysqlconnector/bulkinsert), [BulkMerge](/operation/mysqlconnector/bulkmerge) and [BulkUpdate](/operation/mysqlconnector/bulkupdate) operations of [RepoDb.MySqlConnector.BulkOperations](https://www.nuget.org/packages/RepoDb.MySqlConnector.BulkOperations).

## Create a new Instance

```csharp
var mapItem = new MySqlConnectorBulkInsertMapItem("SourceId", "DestinationId");
```

Or with an explicit `MySqlDbType`:

```csharp
var mapItem = new MySqlConnectorBulkInsertMapItem("SourceName", "DestinationName", MySqlDbType.VarChar);
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new MySqlConnectorBulkInsertMapItem("FirstName", "FName"),
    new MySqlConnectorBulkInsertMapItem("LastName", "LName"),
    new MySqlConnectorBulkInsertMapItem("Age", "Age", MySqlDbType.Int32)
};

using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/mysqlconnector/bulkmerge) and [BulkUpdate](/operation/mysqlconnector/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
