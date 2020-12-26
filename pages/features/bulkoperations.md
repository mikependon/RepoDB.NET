---
layout: default
sidebar: features
title: "Bulk Operations"
description: "It is the process of bringing all the data from the application into the database server at once, and at the same time, ignoring some database specific activities behind the scene. Thus gives you maximum performance during the operation."
permalink: /feature/bulkoperations
tags: [repodb, class, bulk, bulk-operations, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Features
---

# Bulk Operations

---

A bulk operation is a process of bringing all the data from the application into the database server at once, and at the same time, ignoring some database specific activities (i.e.: Logging, Audits, Data-Type Checks, Constraints, etc) behind the scene, thus gives you maximum performance during the operation.

Basically, we normally do the [Delete](/operation/delete), [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update) operations when interacting with the database. Through this, the data is being processed in an atomic way. If we do call the [batch operations](/feature/batchoperations), the multiple single operations are just being batched and executed together, but it does not completely eliminate the round-trips between your application and the database.

Image below shows the data flow of the [BulkInsert](/operation/bulkinsert) operation.

<img src="../../assets/images/site/bulkinsert.svg" />

With the bulk operations, all data is brought from the client application to the database via [BulkInsert](/operation/bulkinsert) process (underneath is the `SqlBulkCopy` class). It ignores the audit, logs, constraints and any other database special handling. Then, the data is being processed together within the database (server).

The bulk operations can improve the performance by more than 90% when processing a large datasets.

> This feature only supports the [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations) RDBMS data provider.

### How does it works?

It is leveraging the ADO.NET `SqlBulkCopy` class of the both `System.Data.SqlClient` and the `Microsoft.Data.SqlClient` namespaces.

For [BulkInsert](/operation/bulkinsert) operation, it simply calls the `WriteToServer()` method to bring all the data into the database. Unless you would like to bring the newly generated identities back to the application after the execution, there is no additional logic is implied.

For the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations, an implied logic and technique has been utilized.

Image below shows the data flow of the [BulkMerge](/operation/bulkmerge) operation.

<img src="../../assets/images/site/bulkmerge.svg" />

Basically, a pseudo-temporary table will be created in the database under the transaction context. It then uses the [BulkInsert](/operation/bulkinsert) operation to target that pseudo-temporary table and process the data afterwards. Through this technique, we brought all the data together from the client application into the database server (at one-go) and process them together at the same time.

You can maximize the execution by targeting your underlying table indexes via qualifiers, simply pass a list of [Field](/class/field) objects. The library will then create a CLUSTERED INDEX on the pseudo-temporary table through the passed qualifiers and do the actual joins to the original table using that index.

> If you have not passed any qualifiers, the primary column will be used by default. If the primary column is not present, it will use the identity column instead.

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
>    INSERT (...) VALUES (...)
> WHEN MATCHED THEN
>    UPDATE
>    SET (...);
```

#### For BulkUpdate

```csharp
> UPDATE T
> SET T.Field3 = TMP.Field3
>     , T.Field4 = TMP.Field4
>     , ...
> FROM [OriginalTable] T
> INNER JOIN [PseudoTempTable] TMP ON TMP.QualiferField1 = T.Field1 AND TMP.QualifierField2 = T.Field2;
```

### Special Arguments

The arguments `qualifiers`, `isReturnIdentity` and `usePhysicalPseudoTempTable` were provided to the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations.

The argument `qualifiers` is used to define the qualifier fields to be used in the operation. It usually refers to the WHERE expression of the SQL Statements. If not given, the primary or identity column will be used.

The argument `isReturnIdentity` is used to define the behaviour of the execution whether the newly generated identities will be set-back to the data entities. By default, it is disabled.

The argument `usePhysicalPseudoTempTable` is used to define whether a physical pseudo-table will be created during the operation. By default, a temporary table (i.e.: `#TableName`) is used.

> Please be noted that it is not recommended to enable the `usePhysicalPseudoTempTable` argument if you are to work with parallelism. Ensure to always utilize the session-based non-physical pseudo-temporary table on parallelism's scenario.

### Caveats

The library is automatically setting the value of `options` argument to `SqlBulkCopyOptions.KeepIdentity` when calling the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) if you have not passed any qualifiers and if your table has an identity primary key column. The same logic will apply if there is no primary key but has an identity column defined in the table.

In addition, when calling the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations, the library is creating a pseudo-temporary table behind the scene. It requires your user to have the proper CREATE TABLE privilege to create a table in the database, otherwise a `SqlException` will be thrown.

### Calling the Operations

Below are the ways on how to call the operations.

#### For BulkDelete

The code snippets below only showcasing the [BulkDelete](/operation/bulkdelete) via IEnumerable&lt;T&gt;.

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

The code snippets below only showcasing the [BulkInsert](/operation/bulkinsert) via IEnumerable&lt;T&gt;.

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

The code snippets below only showcasing the [BulkMerge](/operation/bulkmerge) via IEnumerable&lt;T&gt;.

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

The code snippets below only showcasing the [BulkUpdate](/operation/bulkupdate) via IEnumerable&lt;T&gt;.

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

#### When to use the Batch and Bulk Operations?

There is no standard of when to use what. It all depends on your situation (i.e.: Network Latency, Data, No of Columns, etc).

The pros of using bulk operation is the maximum performance, however, it also keeps blocking the target table while being under the bulk operations transaction. It might trigger a deadlock if not handled properly by the developers.

> We highly recommend to use the batch operations if the number of rows you are working is less than or equal 1000, beyond than that, we highly recommend to always use the bulk operations.
