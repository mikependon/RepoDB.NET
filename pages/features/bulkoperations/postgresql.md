---
layout: default
sidebar: features
title: "PostgreSQL"
description: "It is the process of bringing all the data from the application into the database server at once, and at the same time, ignoring some database specific activities behind the scene. Thus gives you maximum performance during the operation."
permalink: /feature/bulkoperations/postgresql
tags: [repodb, bulk, bulk-operations]
parent: "Bulk Operations"
grand_parent: FEATURES
---

# Bulk Operations (PostgreSQL)

---

The bulk operations implementation is leveraging the existing [NpgsqlBinaryImporter](https://www.npgsql.org/doc/api/Npgsql.NpgsqlBinaryImporter.html) class of [Npgsql](https://www.npgsql.org/) library. A customized method named [BinaryImport](/operation/binaryimport) is introduced to enable this capability, in which, it also utilizes the underlying [Write](https://www.npgsql.org/doc/copy.html) method of raw binary importer class.

The [BinaryBulkInsert](/operation/binarybulkinsert) method, the one that does the bulk insert operation is using the [BinaryImport](/operation/binaryimport) internally. There is no implied logic is introduced on this operation unless the newly generated primary identity column is being requested back to the client right after the execution (see [ReturnIdentity](/enumeration/postgresql/bulkimportidentitybehavior#bulkimportidentitybehavior)).

The image below shows the data flow of the [BinaryBulkInsert](/operation/binarybulkinsert) operation.

<img src="../../assets/images/site/binarybulkinsert.svg" />

For the [BinaryBulkDelete](/operation/binarybulkdelete), [BinaryBulkMerge](/operation/binarybulkmerge) and [BinaryBulkUpdate](/operation/binarybulkupdate) operations, an implied logic is used. Basically, a pseudo-temporary table is being created in the database under a transaction context. The operation will then use the [BinaryImport](/operation/binaryimport) operation to target such pseudo-temporary table and process the data afterwards.

> Through this logic, all the data from the client application is brought to the database server at one-go, and then, being processed together afterwards.

The operations can also be further optimized by targeting the underlying table indexes via the qualifier columns, simply pass the list of [Field](/class/field) objects.

> If the qualifiers are passed, a CLUSTERED INDEX will be created on the pseudo-temporary and it will be used as the qualifiers when merging with the target original table. If no qualifiers are passed, the primary column will be used by default.

### Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;
- ExpandoObject
- IDictionary&lt;string, object&gt;

### Operation SQL Statements

Once all the data is in the database pseudo-temporary table, the correct SQL statement will be used to cascade the changes towards the original table.

#### For BinaryBulkDelete

```csharp
> DELETE FROM "OriginalTable" AS T
> USING "PseudoTempTable" AS S ON S.QualiferField1 = T.Field1 AND T.QualifierField2 = T.Field2;
```

#### For BinaryBulkDeleteByKey

```csharp
> DELETE FROM "OriginalTable" AS T
> USING "PseudoTempTable" AS S ON S.PrimaryKey = T.PrimaryKey;
```

#### For BinaryBulkMerge (InsertAndUpdate)

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

> Disclaimer: The generated statements are not exactly same as the one written above, but the concepts are identical.

#### For BinaryBulkMerge (OnConflictDoUpdate)

```csharp
> INSERT INTO "OriginalTable"
> (...)
> SELECT (...)
> FROM "PseudoTempTable"
> ON CONFLICT (Field1, Field2) DO UPDATE
> SET (...);
```

#### For BinaryBulkUpdate

```csharp
> UPDATE "OriginalTable" AS T
> SET (...)
> FROM "PseudoTempTable" AS S
> WHERE (S.QualiferField1 = T.Field1 AND T.QualifierField2 = T.Field2);
```

## Special Arguments

The arguments `qualifiers`, `keepIdentity`, `identityBehavior`, `pseudoTableType` and `mergeCommanType` were provided in most operations.

The argument `qualifiers` is used to define the qualifier fields to be used in the operations. It usually refers to the `WHERE` expression of SQL Statements. If not given, the primary key field will be used.

The argument `keepIdentity` is used to define a value whether the identity property of the entity/model will be kept during the operation.

The argument `identityBehavior` is used to define a value like with the `keepIdentity` argument, together-with, a value that is used to return the newly generated identity values from the database. 

The argument `pseudoTableType` is used to define a value whether a physical pseudo-table will be created during the operation. By default, a temporary table is used.

The argument `mergedCommandType` is used to define a value whether the existing `ON CONFLICT DO UPDATE` will be used over the `UPDATE/INSERT` SQL commands during operations.

### Identity Setting Alignment

Behind the scene, the library has enforced an additional logic to ensure the identity setting alignment. Basically, a new column named `__RepoDb_OrderColumn` is being added into the pseudo-temporary table if the identity field is present on the underlying table. This column will contain the actual index of the entity model from the `IEnumerable<T>` object.

During the bulk operation, a dedicated index (entity model index) value is passed to this column, thus ensuring that the index value is really equating to the index of the item from the `IEnumerable<T>` object. The resultsets of the pseudo-temporary table are being ordered using this column, prior the actual merge to the underlying table.

For both the [BinaryBulkInsert](https://repodb.net/operation/binarybulkinsert) and [BinaryBulkMerge](https://repodb.net/operation/binarybulkmerge) operations, when the newly generated identity value is being set back to the data model, the value of the `__RepoDb_OrderColumn` column is being used to look-up the proper index of the equating item from the `IEnumerable<T>` object, then, the compiled identity-setter function is used to assign back the identity value into the identity property.

## BatchSize

All the provided operations has a `batchSize` attribute that enables you to override the size of the items being wired-up to the server during the operation. By default it is `null`, all the items are being sent together in one-go.

Use this attribute if you wish to optimize the operation based on certain sitution (i.e.: No. of Columns, Type/Size of Data, Network Latency).

## Async Methods

All the provided synchronous operations has its equivalent asynchronous (Async) operations.

## BinaryBulkDelete

Delete the existing rows from the database by bulk. It returns the number of rows that has been deleted during the operation.

### BinaryBulkDelete via DataEntities

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var deletedRows = connection.BinaryBulkDelete<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var deletedRows = connection.BinaryBulkDelete<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via table-name.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var deletedRows = connection.BinaryBulkDelete("Customer", customers);
}
```

And with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var deletedRows = connection.BinaryBulkDelete("Customer", customers, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

### BinaryBulkDelete via DataTable

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var deletedRows = connection.BinaryBulkDelete("Customer", table);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var deletedRows = connection.BinaryBulkDelete("Customer", table, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

### BinaryBulkDelete via DbDataReader

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var deletedRows = connection.BinaryBulkDelete("Customer", reader);
	}
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var deletedRows = connection.BinaryBulkDelete("Customer", reader, qualifiers: Field.From("LastName", "DateOfBirth"));
	}
}
```

## BinaryBulkDeleteByKey

Delete the existing rows from the database by bulk via a list of primary keys. It returns the number of rows that has been deleted during the operation.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var primaryKeys = new [] { 1, 2, ..., 10045 };
	var deletedRows = connection.BinaryBulkDeleteByKey(primaryKeys);
}
```

## BinaryBulkInsert

Insert a list of entities into the database by bulk. It returns the number of rows that has been inserted in the database.

### BinaryBulkInsert via DataEntities

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var insertedRows = connection.BinaryBulkInsert<Customer>(customers);
}
```

Or via table-name.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var insertedRows = connection.BinaryBulkInsert("Customer", customers);
}
```

### BinaryBulkInsert via DataTable

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var insertedRows = connection.BinaryBulkInsert("Customer", table);
}
```

### BinaryBulkInsert via DbDataReader

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var insertedRows = connection.BinaryBulkInsert("Customer", reader);
	}
}
```

## BinaryBulkMerge

Merge a list of entities into the database by bulk. A new row is being inserted (if not present) and an existing row is being updated (if present) through the defined qualifiers. It returns the number of rows that has been inserted/updated in the database.

### BinaryBulkMerge via DataEntities

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var mergedRows = connection.BinaryBulkMerge<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var mergedRows = connection.BinaryBulkMerge<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via table-name.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var mergedRows = connection.BinaryBulkMerge("Customer", customers);
}
```

And with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var mergedRows = connection.BinaryBulkMerge("Customer", customers, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

### BinaryBulkMerge via DataTable

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var mergedRows = connection.BinaryBulkMerge("Customer", table);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var mergedRows = connection.BinaryBulkMerge("Customer", table, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

### BinaryBulkMerge via DbDataReader

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var mergedRows = connection.BinaryBulkMerge("Customer", reader);
	}
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var mergedRows = connection.BinaryBulkMerge("Customer", reader, qualifiers: Field.From("LastName", "DateOfBirth"));
	}
}
```

## BinaryBulkUpdate

Update the existing rows from the database by bulk. The affected rows are strongly bound to the values of the qualifier fields when calling the operation. It returns the number of rows that has been updated in the database.

### BinaryBulkUpdate via DataEntities

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var rows = connection.BinaryBulkUpdate<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var rows = connection.BinaryBulkUpdate<Customer>(customers, qualifiers: e => new { e.LastName, e.DateOfBirth });
}
```

Or via table-name.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var rows = connection.BinaryBulkUpdate("Customer", customers);
}
```

And with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var customers = GetCustomers();
	var rows = connection.BinaryBulkUpdate("Customer", customers, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

### BinaryBulkUpdate via DataTable

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var rows = connection.BinaryBulkUpdate("Customer", table);
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	var table = GetCustomersAsDataTable();
	var rows = connection.BinaryBulkUpdate("Customer", table, qualifiers: Field.From("LastName", "DateOfBirth"));
}
```

### BinaryBulkUpdate via DbDataReader

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var rows = connection.BinaryBulkUpdate("Customer", reader);
	}
}
```

Or with qualifiers.

```csharp
using (var connection = new NpgsqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Customer];"))
	{
		var rows = connection.BinaryBulkUpdate("Customer", reader, qualifiers: Field.From("LastName", "DateOfBirth"));
	}
}
```
