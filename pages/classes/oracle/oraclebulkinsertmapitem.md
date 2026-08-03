---
layout: default
sidebar: classes
title: "OracleBulkInsertMapItem"
description: "A mapping class used to define a column mapping, with an optional explicit OracleDbType, for the Oracle bulk operations."
permalink: /class/oracle/oraclebulkinsertmapitem
tags: [repodb, oraclebulkinsertmapitem, oracle, bulk]
parent: "Oracle"
grand_parent: CLASSES
---

# OracleBulkInsertMapItem

---

This class extends [BulkInsertMapItem](/class/bulkinsertmapitem) with an optional, explicit `OracleDbType` to bind with for the mapped column. When not provided, the type is inferred from the entity property's [OracleDbType](/attribute/oracle/oracledbtype)/[OracleDbTypeEx](/attribute/oracle/oracledbtypeex) attribute (if present) or, failing that, from the .NET CLR value itself.

Used by the Oracle [BulkInsert](/operation/oracle/bulkinsert), [BulkMerge](/operation/oracle/bulkmerge) and [BulkUpdate](/operation/oracle/bulkupdate) operations.

## Create a new Instance

```csharp
var mapItem = new OracleBulkInsertMapItem("SourceId", "DestinationId");
```

Or with an explicit `OracleDbType`:

```csharp
var mapItem = new OracleBulkInsertMapItem("SourceName", "DestinationName", OracleDbType.NVarchar2);
```

## Usage for BulkOperations

```csharp
var mappings = new []
{
    new OracleBulkInsertMapItem("FirstName", "FName"),
    new OracleBulkInsertMapItem("LastName", "LName"),
    new OracleBulkInsertMapItem("Age", "Age", OracleDbType.Int32)
};

using (var connection = new OracleConnection(connectionString))
{
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkMerge](/operation/oracle/bulkmerge) and [BulkUpdate](/operation/oracle/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
