---
layout: default
title: "PostgreSQL"
nav_order: 4
has_children: true
permalink: /operation/postgresql
parent: OPERATIONS
---

# Operations (PostgreSQL)

---

The bulk operations implementation is leveraging the existing [NpgsqlBinaryImporter](https://www.npgsql.org/doc/api/Npgsql.NpgsqlBinaryImporter.html) class of [Npgsql](https://www.npgsql.org/) library. A customized method named [BinaryImport](/operation/postgresql/binaryimport) is introduced to enable this capability, in which, it also utilizes the underlying [Write](https://www.npgsql.org/doc/copy.html) method of raw binary importer class.

The [BulkInsert](/operation/postgresql/bulkinsert) method, the one that does the bulk insert operation is using the [BinaryImport](/operation/postgresql/binaryimport) internally. There is no implied logic is introduced on this operation unless the newly generated primary identity column is being requested back to the client right after the execution (see [ReturnIdentity](/enumeration/postgresql/postgresqlbulkimportidentitybehavior#postgresqlbulkimportidentitybehavior)).

For the [BulkDelete](/operation/postgresql/bulkdelete), [BulkMerge](/operation/postgresql/bulkmerge) and [BulkUpdate](/operation/postgresql/bulkupdate) operations, an implied logic is used. Basically, a pseudo-temporary table is being created in the database under a transaction context. The operation will then use the [BinaryImport](/operation/postgresql/binaryimport) operation to target such pseudo-temporary table and process the data afterwards.

{: .note }
> Through this logic, all the data from the client application is brought to the database server at one-go, and then, being processed together afterwards.

The operations can also be further optimized by targeting the underlying table indexes via the qualifier columns, simply pass the list of [Field](/class/field) objects.

{: .important }
> If the qualifiers are passed, a CLUSTERED INDEX will be created on the pseudo-temporary and it will be used as the qualifiers when merging with the target original table. If no qualifiers are passed, the primary column will be used by default.

## Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;
- ExpandoObject
- IDictionary&lt;string, object&gt;

## Operation SQL Statements

Once all the data is in the database pseudo-temporary table, the correct SQL statement will be used to cascade the changes towards the original table.

#### For BulkDelete

```csharp
> DELETE FROM "OriginalTable" AS T
> USING "PseudoTempTable" AS S ON S.QualiferField1 = T.Field1 AND T.QualifierField2 = T.Field2;
```

#### For BulkDeleteByKey

```csharp
> DELETE FROM "OriginalTable" AS T
> USING "PseudoTempTable" AS S ON S.PrimaryKey = T.PrimaryKey;
```

#### For BulkMerge (InsertAndUpdate)

```csharp
> UPDATE "OriginalTable" AS T
> SET (...)
> FROM "PseudoTempTable" AS S
> WHERE (S.QualiferField1 = T.Field1 AND T.QualifierField2 = T.Field2);

> INSERT INTO "OriginalTable"
> (...)
> SELECT (...)
> FROM "PseudoTempTable"
> WHERE NOT (S.QualiferField1 = T.Field1 AND T.QualifierField2 = T.Field2);
```

{: .note }
> Disclaimer: The actual generated statements are not exactly the same as the one written above, but the concepts are identical.

#### For BulkMerge (OnConflictDoUpdate)

```csharp
> INSERT INTO "OriginalTable"
> (...)
> SELECT (...)
> FROM "PseudoTempTable"
> ON CONFLICT (Field1, Field2) DO UPDATE
> SET (...);
```

#### For BulkUpdate

```csharp
> UPDATE "OriginalTable" AS T
> SET (...)
> FROM "PseudoTempTable" AS S
> WHERE (S.QualiferField1 = T.Field1 AND T.QualifierField2 = T.Field2);
```

## Special Arguments

The arguments below were provided in most operations.

| Argument | Description |
|:---------|:------------|
| `qualifiers` | Defines the qualifier fields to be used in the operations. It usually refers to the `WHERE` expression of SQL Statements. If not given, the primary key field will be used. |
| `identityBehavior` | Defines a value whether the identity property of the entity/model will be kept, or whether the newly generated identity values from the database are returned back to the entities. |
| `pseudoTableType` | Defines a value whether a physical pseudo-table will be created during the operation. By default, a temporary table is used. |
| `mergeCommandType` | Defines a value whether the existing `ON CONFLICT DO UPDATE` will be used over the `UPDATE`/`INSERT` SQL commands during operations. |

## Identity Setting Alignment

Behind the scene, the library has enforced an additional logic to ensure the identity setting alignment. Basically, a new column named `__RepoDb_OrderColumn` is being added into the pseudo-temporary table if the identity field is present on the underlying table. This column will contain the actual index of the entity model from the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object.

During the bulk operation, a dedicated index (entity model index) value is passed to this column, thus ensuring that the index value is really equating to the index of the item from the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object. The resultsets of the pseudo-temporary table are being ordered using this column, prior the actual merge to the underlying table.

For both the [BulkInsert](https://repodb.net/operation/postgresql/bulkinsert) and [BulkMerge](https://repodb.net/operation/postgresql/bulkmerge) operations, when the newly generated identity value is being set back to the data model, the value of the `__RepoDb_OrderColumn` column is being used to look-up the proper index of the equating item from the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object, then, the compiled identity-setter function is used to assign back the identity value into the identity property.

## BatchSize

All the provided operations has a `batchSize` attribute that enables you to override the size of the items being wired-up to the server during the operation. By default it is `null`, all the items are being sent together in one-go.

Use this attribute if you wish to optimize the operation based on certain situtions.

- Network Latency
- Infrastructure
- No. of Columns
- Type of Data

## Async Methods

All the provided synchronous operations has its equivalent asynchronous (Async) operations.

---

## BulkDelete

Delete the existing rows from the database by bulk. It returns the number of rows that has been deleted during the operation.

#### BulkDelete (via DataEntities)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var deletedRows = connection.BulkDelete<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var deletedRows = connection.BulkDelete<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via table-name.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var deletedRows = connection.BulkDelete("Customer", customers);
}
```

And with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var deletedRows = connection.BulkDelete("Customer", customers, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

#### BulkDelete (via DataTable)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var deletedRows = connection.BulkDelete("Customer", table);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var deletedRows = connection.BulkDelete("Customer", table, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

#### BulkDelete (via DbDataReader)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var deletedRows = connection.BulkDelete("Customer", reader);
	}
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var deletedRows = connection.BulkDelete("Customer", reader, qualifiers: Field.From("LastName", "DateOfBirth"));
	}
}
```

---

## BulkDeleteByKey

Delete the existing rows from the database by bulk via a list of primary keys. It returns the number of rows that has been deleted during the operation.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var primaryKeys = new [] { 1, 2, ..., 10045 };
	var deletedRows = connection.BulkDeleteByKey(primaryKeys);
}
```

---

## BulkInsert

Insert a list of entities into the database by bulk. It returns the number of rows that has been inserted in the database.

#### BulkInsert (via DataEntities)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var insertedRows = connection.BulkInsert<Customer>(customers);
}
```

Or via table-name.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var insertedRows = connection.BulkInsert("Customer", customers);
}
```

#### BulkInsert (via DataTable)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var insertedRows = connection.BulkInsert("Customer", table);
}
```

#### BulkInsert (via DbDataReader)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var insertedRows = connection.BulkInsert("Customer", reader);
	}
}
```

---

## BulkMerge

Merge a list of entities into the database by bulk. A new row is being inserted (if not present) and an existing row is being updated (if present) through the defined qualifiers. It returns the number of rows that has been inserted/updated in the database.

#### BulkMerge (via DataEntities)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var mergedRows = connection.BulkMerge<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var mergedRows = connection.BulkMerge<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via table-name.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var mergedRows = connection.BulkMerge("Customer", customers);
}
```

And with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var mergedRows = connection.BulkMerge("Customer", customers, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

#### BulkMerge (via DataTable)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var mergedRows = connection.BulkMerge("Customer", table);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var mergedRows = connection.BulkMerge("Customer", table, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

#### BulkMerge (via DbDataReader)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var mergedRows = connection.BulkMerge("Customer", reader);
	}
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var mergedRows = connection.BulkMerge("Customer", reader, qualifiers: Field.From("LastName", "DateOfBirth"));
	}
}
```

---

## BulkUpdate

Update the existing rows from the database by bulk. The affected rows are strongly bound to the values of the qualifier fields when calling the operation. It returns the number of rows that has been updated in the database.

#### BulkUpdate (via DataEntities)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkUpdate<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkUpdate<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via table-name.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkUpdate("Customer", customers);
}
```

And with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkUpdate("Customer", customers, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

#### BulkUpdate (via DataTable)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var rows = connection.BulkUpdate("Customer", table);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var rows = connection.BulkUpdate("Customer", table, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

#### BulkUpdate (via DbDataReader)

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var rows = connection.BulkUpdate("Customer", reader);
	}
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var rows = connection.BulkUpdate("Customer", reader, qualifiers: Field.From("LastName", "DateOfBirth"));
	}
}
```
