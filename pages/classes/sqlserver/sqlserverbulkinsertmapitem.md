---
layout: default
sidebar: classes
title: "SqlServerBulkInsertMapItem"
description: "A mapping class used to define a column mapping for the SQL Server bulk operations."
permalink: /class/sqlserver/sqlserverbulkinsertmapitem
tags: [repodb, sqlserverbulkinsertmapitem, sqlserver, bulk]
parent: "SQL Server"
grand_parent: CLASSES
---

# SqlServerBulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) to provide a SQL Server-specific type for column mappings, keeping the mapping API consistent with the [PostgreSQL](/class/postgresql/postgresqlbulkinsertmapitem) and [Oracle](/class/oracle/oraclebulkinsertmapitem) bulk operations.

Used by the SQL Server [BulkInsert](/operation/sqlserver/bulkinsert), [BulkMerge](/operation/sqlserver/bulkmerge), [BulkUpdate](/operation/sqlserver/bulkupdate) and [BulkDelete](/operation/sqlserver/bulkdelete) operations.

## Create a new Instance

```csharp
var mapItem = new SqlServerBulkInsertMapItem("SourceId", "DestinationId");
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new SqlServerBulkInsertMapItem("FirstName", "FName"),
    new SqlServerBulkInsertMapItem("LastName", "LName"),
    new SqlServerBulkInsertMapItem("Age", "Age")
};

using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/sqlserver/bulkmerge), [BulkUpdate](/operation/sqlserver/bulkupdate) and [BulkDelete](/operation/sqlserver/bulkdelete). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive). The base [BulkInsertMapItem](/class/bulkinsertmapitem) class is equally accepted wherever `SqlServerBulkInsertMapItem` is.
