---
layout: default
sidebar: operations
title: "BulkInsert"
permalink: /operation/mariadbconnector/bulkinsert
tags: [repodb, tutorial, bulkinsert, orm, hybrid-orm, mariadb, mariadbconnector]
parent: "MariaDbConnector"
grand_parent: OPERATIONS
---

# BulkInsert

---

This method inserts all rows from the client application into the database in bulk. It is supported for [RepoDb.MariaDbConnector.BulkOperations](https://www.nuget.org/packages/RepoDb.MariaDbConnector.BulkOperations), targeting the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector)-based driver.

{: .note }
> This page documents the `RepoDb.MariaDbConnector` (`MySqlConnector`-based) arguments and examples. For the `MySql.Data`-based implementation, see [BulkInsert (MariaDb)](/operation/mariadb/bulkinsert); for MySqlConnector's own MySQL provider, see [BulkInsert (MySqlConnector)](/operation/mysqlconnector/bulkinsert).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

```mermaid
flowchart TD
    Client["Client<br/>(RepoDB)"] -->|BulkInsert| Source["Entities /<br/>DataTable /<br/>DbDataReader"]
    Source --> Decision{"identityBehavior ==<br/>ReturnIdentity?"}
    Decision -->|NO| Direct["MariaDbBulkCopy<br/>(excludes identity column)"]
    Direct -->|Write| Table[("Target Table<br/>AUTO_INCREMENT generates Id")]
    Decision -->|YES| Pseudo["Create Pseudo Table<br/>(Physical)"]
    Pseudo --> Staged["MariaDbBulkCopy"]
    Staged -->|Write| PseudoTable[("Pseudo Table")]
    PseudoTable --> Generate["Pre-assign identity per staged row<br/>(session variable, seeded from MAX(Id)+1)"]
    Generate --> Insert["INSERT INTO ... SELECT<br/>(identities already staged)"]
    Insert --> Table
    Insert -->|"SELECT identity<br/>ORDER BY __RepoDbBulkRowOrder__"| Client
    PseudoTable -->|Drop| Cleanup(["Pseudo Table Dropped"])
```

## Use Case

Use this method to insert rows at high speed. It leverages `RepoDb.Connector.MariaDbConnector`'s own `MariaDbBulkCopy` class, the MariaDB-flavored counterpart to `SqlBulkCopy`, built on top of [MySqlConnector](https://www.nuget.org/packages/MySqlConnector).

For inserting 1,000 or more rows, prefer this method over [InsertAll](/operation/insertall).

Rows are written straight to the target table. A staging table is only used when `identityBehavior` is set to `ReturnIdentity` (see below) — see [Operations (MariaDbConnector)](/operation/mariadbconnector) for the underlying mechanics.

## Special Arguments

The `mappings`, `bulkCopyTimeout`, `batchSize`, `identityBehavior` and `pseudoTableType` arguments are available for this operation.

`mappings` (via `MariaDbBulkInsertMapItem`) defines explicit column mappings between the source properties and the destination columns. When omitted, columns are auto-mapped by name (case-insensitive) — the identity column is left out of this auto-mapping unless `identityBehavior` is `ReturnIdentity`, so `AUTO_INCREMENT` generates it as usual.

`bulkCopyTimeout` overrides the command timeout, in seconds.

`batchSize` overrides the number of rows sent to the server per batch. When not set, all items are sent at once.

`identityBehavior` (via [MariaDbBulkImportIdentityBehavior](/enumeration/mariadb/mariadbconnector/mariadbbulkimportidentitybehavior)) controls whether newly generated identity values are set back on the data entities. Defaults to `KeepIdentity`, which excludes the identity column from the write entirely. Setting it to `ReturnIdentity` routes the operation through a staging table instead, so the generated values can be pre-assigned and read back — see [Identity Setting Alignment](#identity-setting-alignment) below.

`pseudoTableType` (via [MariaDbBulkImportPseudoTableType](/enumeration/mariadb/mariadbconnector/mariadbbulkimportpseudotabletype)) controls the kind of staging table used when `identityBehavior` is `ReturnIdentity`.

{: .important }
> Every `pseudoTableType` value currently resolves to `Physical` at runtime — see [Operations (MariaDbConnector)](/operation/mariadbconnector#pseudo-table-type) for details.

## Identity Setting Alignment

When `identityBehavior` is `ReturnIdentity`, the library adds a surrogate `__RepoDbBulkRowOrder__` column to the staging table to track each entity's position in the source [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0). Identity values are pre-assigned into the staging table via a session user variable seeded from the target table's current `MAX(identity) + 1` (read live off the table, not from `information_schema`'s cached `AUTO_INCREMENT` value), then copied over as literal values during the final `INSERT INTO ... SELECT`, ensuring the identity values returned from the database are assigned back to the correct entity via the compiled identity-setter function.

{: .important }
> This requires `AllowUserVariables=True` on your MariaDbConnector connection string — it is `false` by default.

## Usability

The following example defines a method that produces a list of `Person` objects, then bulk-inserts 10,000 rows into the `Person` table.

```csharp
private IEnumerable<Person> GetPeople(int count = 1000)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Person
        {
            Name = $"Person-{i}",
            SSN = Guid.NewGuid().ToString(),
            IsActive = true,
            DateInsertedUtc = DateTime.UtcNow
        };
    }
}
```

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people);
}
```

To specify a batch size:

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people, batchSize: 100);
}
```

{: .note }
> When `batchSize` is not set, all rows are sent to the server in a single batch.

To return the newly generated identity values:

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: MariaDbBulkImportIdentityBehavior.ReturnIdentity);
}
```

#### DataTable

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var table = ConvertToDataTable(people);
    var insertedRows = connection.BulkInsert("Person", table);
}
```

#### Dictionary/ExpandoObject

```csharp
using (var sourceConnection = new MariaDbConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new MariaDbConnection(destinationConnectionString))
    {
        var insertedRows = destinationConnection.BulkInsert("Person", result);
    }
}
```

#### DataReader

```csharp
using (var sourceConnection = new MariaDbConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM Person"))
    {
        using (var destinationConnection = new MariaDbConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkInsert("Person", reader);
        }
    }
}
```

To bulk-insert via [DataEntityDataReader](/class/dataentitydatareader):

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var insertedRows = connection.BulkInsert("Person", reader);
    }
}
```

## Column Mappings

Add column mappings using the `MariaDbBulkInsertMapItem` class.

```csharp
var mappings = new List<MariaDbBulkInsertMapItem>();

// Add the mappings
mappings.Add(new MariaDbBulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new MariaDbBulkInsertMapItem("SourceName", "DestinationName"));
mappings.Add(new MariaDbBulkInsertMapItem("SourceIsActive", "DestinationIsActive"));
mappings.Add(new MariaDbBulkInsertMapItem("SourceDateInsertedUtc", "DestinationDateInsertedUtc"));

// Execute
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert(people,
        mappings: mappings);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert("Person", people);
}
```

## Async Method

An equivalent [BulkInsertAsync](/operation/mariadbconnector/bulkinsert) method is also available.

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(10000);
    var insertedRows = await connection.BulkInsertAsync(people);
}
```
