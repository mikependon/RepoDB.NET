---
layout: default
title: "EnterpriseDB"
has_children: true
permalink: /operation/enterprisedb
parent: OPERATIONS
---

# Operations (EnterpriseDB)

---

RepoDB's standard operations ([Query](/operation/query), [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update), [Delete](/operation/delete), etc.) all work against `EDBConnection` once [UseEnterpriseDb()](/class/enterprisedb/enterprisedbglobalconfiguration) has been called. `BulkInsert`, `BulkMerge`, `BulkUpdate`, `BulkDelete` and `BulkDeleteByKey` are provided by the separate [RepoDb.EnterpriseDb.BulkOperations](https://www.nuget.org/packages/RepoDb.EnterpriseDb.BulkOperations) package.

Rows are staged through a pseudo (staging) table and loaded via `RepoDb.Connector.EnterpriseDb`'s `EDBBulkCopy`, itself built on top of Npgsql's native binary `COPY` protocol — so, unlike SAP HANA, this is a genuine bulk load rather than a client-side loop of single-row statements. Once staged, a single SQL statement cascades the change to the target table.

[BulkInsert](#bulkinsert) writes straight into the target table and skips the staging table entirely, unless [EDBBulkImportIdentityBehavior.ReturnIdentity](/enumeration/enterprisedb/edbbulkimportidentitybehavior) is requested, in which case a pseudo table is used first so the generated identity values can be read back.

The other operations can be further optimized by targeting the underlying table indexes via the qualifier columns — pass a list of [Field](/class/field) objects.

## Pseudo Table Type

See [EDBBulkImportPseudoTableType](/enumeration/enterprisedb/edbbulkimportpseudotabletype) for the full detail on `Auto`, `Memory`, and `Physical`, and the concurrency caveat with deterministic pseudo-table names.

## Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;
- ExpandoObject
- IDictionary&lt;string, object&gt;

## Operation SQL Statements

Once all the data is in the pseudo (staging) table, a single SQL statement cascades the changes towards the target table.

#### For BulkInsert (ReturnIdentity only)

```csharp
> INSERT INTO "OriginalTable" (Field1, Field2, ...)
> SELECT Field1, Field2, ... FROM "PseudoTable" ORDER BY "__RepoDbBulkRowOrder__"
> RETURNING "Id" AS "Result";
```

#### For BulkMerge (KeepIdentity)

```csharp
> UPDATE "OriginalTable" SET Field3 = S.Field3, Field4 = S.Field4
> FROM "PseudoTable" S WHERE (OriginalTable.QualifierField1 = S.QualifierField1);
>
> INSERT INTO "OriginalTable" (Field1, Field2, ...)
> SELECT Field1, Field2, ... FROM "PseudoTable" S
> WHERE NOT EXISTS (SELECT 1 FROM "OriginalTable" WHERE QualifierField1 = S.QualifierField1)
> ORDER BY S."__RepoDbBulkRowOrder__";
```

#### For BulkMerge (ReturnIdentity)

A single `ON CONFLICT DO UPDATE` upsert, preceded by pre-assigning the next identity sequence value to every row that has no existing match:

```csharp
> UPDATE "PseudoTable" SET "Id" = nextval(pg_get_serial_sequence('"OriginalTable"', 'Id'))
> WHERE NOT EXISTS (SELECT 1 FROM "OriginalTable" WHERE QualifierField1 = "PseudoTable".QualifierField1);
>
> INSERT INTO "OriginalTable" (Id, Field1, Field2, ...) OVERRIDING SYSTEM VALUE
> SELECT Id, Field1, Field2, ... FROM "PseudoTable" ORDER BY "__RepoDbBulkRowOrder__"
> ON CONFLICT (QualifierField1) DO UPDATE SET Field3 = EXCLUDED.Field3, Field4 = EXCLUDED.Field4
> RETURNING "Id" AS "Result";
```

#### For BulkUpdate

```csharp
> UPDATE "OriginalTable" SET Field3 = S.Field3, Field4 = S.Field4
> FROM "PseudoTable" S WHERE (OriginalTable.QualifierField1 = S.QualifierField1);
```

{: .note }
> Unlike [BulkMerge](#bulkmerge), staged rows with no matching target row are left as-is, not inserted.

#### For BulkDelete / BulkDeleteByKey

```csharp
> DELETE FROM "OriginalTable" USING "PseudoTable" S
> WHERE (OriginalTable.QualifierField1 = S.QualifierField1);
```

## Special Arguments

The arguments below are available on most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the fields used to match existing rows. Defaults to the primary or identity column when not provided. |
| `identityBehavior` | Via [EDBBulkImportIdentityBehavior](/enumeration/enterprisedb/edbbulkimportidentitybehavior), controls whether the identity property is kept as-is, or whether newly generated identity values are returned back to the entities after [BulkInsert](#bulkinsert) or [BulkMerge](#bulkmerge). |
| `pseudoTableType` | Via [EDBBulkImportPseudoTableType](/enumeration/enterprisedb/edbbulkimportpseudotabletype), controls the kind of staging table created — see [Pseudo Table Type](#pseudo-table-type) above. |
| `mappings` | Via [EDBBulkInsertMapItem](/class/enterprisedb/edbbulkinsertmapitem), defines explicit source-to-destination column mappings, with an optional `EDBType` override. Omitting it auto-maps columns by name (case-insensitive). |
| `bulkCopyTimeout` | Overrides the command timeout, in seconds, applied to the underlying `EDBBulkCopy` write. |
| `batchSize` | Overrides how many rows `EDBBulkCopy` buffers client-side per flush. |

## Identity Setting Alignment

When the identity field is present, the pseudo table gets an extra `__RepoDbBulkRowOrder__` identity column recording each row's original position in the source `IEnumerable`/`DataTable`. After [BulkInsert](#bulkinsert) or [BulkMerge](#bulkmerge) runs with `identityBehavior: ReturnIdentity`, results are read back `ORDER BY "__RepoDbBulkRowOrder__"`, and that ordering is used to assign each newly generated identity value onto the matching entity or `DataRow`.

## Async Methods

All the provided synchronous operations have an equivalent asynchronous (`Async`) counterpart.

---

## BulkInsert

Inserts a list of entities into the database in bulk. Returns the number of inserted rows.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var insertedRows = connection.BulkInsert<Customer>(customers);
}
```

Or via table-name:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var insertedRows = connection.BulkInsert("Customer", customers);
}
```

Or via a `DataTable`:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var table = GetCustomersAsDataTable();
    var insertedRows = connection.BulkInsert("Customer", table);
}
```

Returning generated identities:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers(); // Id not set
    connection.BulkInsert<Customer>(customers, identityBehavior: EDBBulkImportIdentityBehavior.ReturnIdentity);
    // customers[i].Id now holds the generated identity for each row
}
```

---

## BulkMerge

Upserts a list of entities in bulk — inserts new rows and updates existing ones based on the defined qualifiers. Returns the number of affected rows.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var mergedRows = connection.BulkMerge<Customer>(customers);
}
```

Or with qualifiers:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var mergedRows = connection.BulkMerge<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via table-name with qualifiers:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var mergedRows = connection.BulkMerge("Customer", customers, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

Or via a `DataTable`:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var table = GetCustomersAsDataTable();
    var mergedRows = connection.BulkMerge("Customer", table);
}
```

---

## BulkUpdate

Updates existing rows in the database in bulk, matched by the defined qualifiers. Returns the number of updated rows.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var rows = connection.BulkUpdate<Customer>(customers);
}
```

Or with qualifiers:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var rows = connection.BulkUpdate<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via a `DataTable`:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var table = GetCustomersAsDataTable();
    var rows = connection.BulkUpdate("Customer", table);
}
```

---

## BulkDelete

Deletes existing rows from the database in bulk, matched by the defined qualifiers. Returns the number of deleted rows.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var deletedRows = connection.BulkDelete<Customer>(customers);
}
```

Or with qualifiers:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var customers = GetCustomers();
    var deletedRows = connection.BulkDelete<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via a `DataTable`:

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var table = GetCustomersAsDataTable();
    var deletedRows = connection.BulkDelete("Customer", table);
}
```

---

## BulkDeleteByKey

Deletes existing rows from the database in bulk, matched by their primary (or identity) key value alone — no entities or `DataTable` involved, just the list of key values to remove. Returns the number of deleted rows.

```csharp
using (var connection = new EDBConnection(ConnectionString))
{
    var primaryKeys = new [] { 10045, 10046, 10047 };
    var deletedRows = connection.BulkDeleteByKey("Customer", primaryKeys);
}
```
