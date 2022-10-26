---
layout: default
sidebar: features
title: "SQL Server"
description: "It is the process of bringing all the data from the application into the database server at once, and at the same time, ignoring some database specific activities behind the scene. Thus gives you maximum performance during the operation."
permalink: /feature/bulkoperations/sqlserver
tags: [repodb, bulk, bulk-operations]
parent: "Bulk Operations"
grand_parent: FEATURES
---

# Bulk Operations (SQL Server)

---

For SQL Server, the underlying implementation is leveraging the existing ADO.NET `SqlBulkCopy` class of the `Microsoft.Data.SqlClient` namespace.

For [BulkInsert](/operation/bulkinsert) operation, it simply calls the `WriteToServer()` method to bring all the data into the database. Unless you would like to bring the newly generated identities back to the application after the execution, there is no additional logic is implied.

Image below shows the data flow of the [BulkInsert](/operation/bulkinsert) operation.

<img src="../../assets/images/site/bulkinsert.svg" />

For the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations, an implied logic and technique has been utilized.

Image below shows the data flow of the [BulkMerge](/operation/bulkmerge) operation.

<img src="../../assets/images/site/bulkmerge.svg" />

Basically, a pseudo-temporary table will be created in the database under a transaction context. It then uses the [BulkInsert](/operation/bulkinsert) operation to target that pseudo-temporary table and process the data afterwards. Through this technique, we brought all the data together from the client application into the database server (at one-go) and process them together at the same time.

For the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations, you can maximize the execution by targeting your underlying table indexes via qualifiers, simply pass a list of [Field](/class/field) object. The library will then create a CLUSTERED INDEX on the pseudo-temporary table through the passed qualifiers and do the actual joins to the original table using that index.

> If you have not passed any qualifiers, the primary column will be used by default. If the primary column is not present, it will use the identity column.

### Supported Objects

Below are the following objects supported by the bulk operations.

- System.DataTable
- System.Data.Common.DbDataReader
- IEnumerable&lt;T&gt;
- ExpandoObject
- IDictionary&lt;string, object&gt;

### Operation SQL Statements

Once all the data is in the database pseudo-temporary table, the correct SQL statement will be used to cascade the changes towards the original table.

#### For BulkDelete

```csharp
> DELETE T
> FROM [dbo].[OriginalTable] T
> INNER JOIN [PseudoTempTable] TMP ON TMP.QualiferField1 = T.Field1 AND TMP.QualifierField2 = T.Field2;
```

#### For BulkMerge

```csharp
> MERGE [dbo].[OriginalTable] T
> USING [PseudoTempTable] S ON S.QualiferField1 = T.Field1 AND S.QualifierField2 = T.Field2
> WHEN NOT MATCHED THEN
> INSERT (...) VALUES (...)
> WHEN MATCHED THEN
> UPDATE
> SET (...);
```

#### For BulkUpdate

```csharp
> UPDATE T
> SET T.Field3 = TMP.Field3
> , T.Field4 = TMP.Field4
> , ...
> FROM [OriginalTable] T
> INNER JOIN [PseudoTempTable] TMP ON TMP.QualiferField1 = T.Field1 AND TMP.QualifierField2 = T.Field2;
```

### Special Arguments

The arguments `qualifiers`, `isReturnIdentity` and `usePhysicalPseudoTempTable` were provided to the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations.

The argument `qualifiers` is used to define the qualifier fields to be used in the operation. It usually refers to the WHERE expression of the SQL Statement. If not given, the primary column will be used.

The argument `isReturnIdentity` is used to define the behaviour of the execution whether the newly generated identities will be set-back to the data entities. By default, it is disabled.

The argument `usePhysicalPseudoTempTable` is used to define whether a physical pseudo-table will be created during the operation. By default, a temporary table (i.e.: `#TableName`) is used.

> Please be noted that it is not recommended to enable the `usePhysicalPseudoTempTable` argument if you are to work with parallelism. Ensure to always utilize the session-based non-physical pseudo-temporary table when working with parallelism.

### Identity Setting Alignment

The library has enforced an additional logic to ensure the identity setting alignment if the `isReturnIdentity` is enabled during the calls. This affects both the [BulkInsert](/operation/bulkinsert) and [BulkMerge](/operation/bulkmerge) operations.

Basically, a new column named `__RepoDb_OrderColumn` is being added into the pseudo-temporary table if the identity field is present on the underlying target table. This column will contain the actual index of the entity model from the `IEnumerable<T>` object.

During the bulk operation, a dedicated `DbParameter` object is created that targets this additional column with a value of the entity model index, thus ensuring that the index value is really equating the index of the entity data from the `IEnumerable<T>` object. The resultsets of the pseudo-temporary table are being ordered using this newly generated column prior the actual merge to the underlying table.

When the newly generated identity value is being set back to the data model, the value of the `__RepoDb_OrderColumn` column is being used to look-up the proper index of the equating entity model from the `IEnumerable<T>` object, then, the compiled identity-setter function is used to assign back the identity value into the identity property.

### Caveats

The library is automatically setting the value of the `options` argument to `SqlBulkCopyOptions.KeepIdentity` when calling the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) if you have not passed any qualifiers and if your table has an identity key column.

In addition, when calling the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations, the library is creating a pseudo-temporary table behind the scene. It requires your user to have the proper CREATE TABLE privilege to create a table in the database, otherwise a `SqlException` will be thrown.

### Calling the Operations

Below are the ways on how to call the operations.

#### For BulkDelete

The code snippets below only showcasing the [BulkDelete](/operation/bulkdelete) via `IEnumerable<T>`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var rows = connection.BulkDelete<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var qualifiers = Field.Parse<Customer>(e => new { e.LastName, e.BirthDate });
 var rows = connection.BulkDelete<Customer>(customers, qualifiers: qualifiers);
}
```

Or via primary keys.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var keys = new object[] { 10045, ..., 11211 }
 var rows = connection.BulkDelete<Customer>(keys);
}
```

You can also target via table-name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var rows = connection.BulkDelete("[dbo].[Customer]", customers);
}
```

#### For BulkInsert

The code snippets below only showcasing the [BulkInsert](/operation/bulkinsert) via `IEnumerable<T>`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var rows = connection.BulkInsert<Customer>(customers);
}
```

Or via table-name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var rows = connection.BulkInsert("[dbo].[Customer]", customers);
}
```

#### For BulkMerge

The code snippets below only showcasing the [BulkMerge](/operation/bulkmerge) via `IEnumerable<T>`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var rows = connection.BulkMerge<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var qualifiers = Field.Parse<Customer>(e => new { e.LastName, e.BirthDate });
 var rows = connection.BulkMerge<Customer>(customers, qualifiers: qualifiers);
}
```

You can also target via table-name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var rows = connection.BulkMerge("[dbo].[Customer]", customers);
}
```

#### For BulkUpdate

The code snippets below only showcasing the [BulkUpdate](/operation/bulkupdate) via `IEnumerable<T>`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var rows = connection.BulkUpdate<Customer>(customers);
}
```

Or with qualifiers.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var qualifiers = Field.Parse<Customer>(e => new { e.LastName, e.BirthDate });
 var rows = connection.BulkUpdate<Customer>(customers, qualifiers: qualifiers);
}
```

You can also target via table-name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
 var customers = GetCustomers();
 var rows = connection.BulkUpdate("[dbo].[Customer]", customers);
}
```
