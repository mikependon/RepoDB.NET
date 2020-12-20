---
layout: post
title: "C#: Bulk Operations for .NET Core (BulkDelete, BulkInsert, BulkMerge, BulkUpdate)"
author: "Michael Camara Pendon"
date: 2020-04-06 13:00:00 +0100
categories: blogs repodb
---

#### Introduction

We would like to share you the latest and greatest updates of RepoDB ORM. Recently, we just published the complete out-of-the-box support of Bulk Operations for SQL Server in .NET data access space.

The actual repository can be found at [RepoDb.SqlServer.BulkOperations](https://github.com/mikependon/RepoDb/tree/master/RepoDb.Extensions/RepoDb.SqlServer.BulkOperations).

###### The updates cover the following awaited methods

- `BulkDelete` (`BulkDeleteAsync`)
- `BulkInsert` (`BulkInsertAsync`)
- `BulkMerge` (`BulkMergeAsync`)
- `BulkUpdate` (`BulkUpdateAsync`)

If you are usually working with large datasets, then this feature is for you. By using this, it will drastically improve the performance of your application. Together-with, increasing your productivity during the development.

In addition, it will remove your worries by writing and maintaining the complex and tedious code snippets (implementation complexity) just to achieve your "BASIC" goals with high-performance.

*As an author and being an OSS contributor, I am trying to improve more the experience of most .NET Developers when it comes to data accessibility. The features, within the library is free and will always be. In return, I only like to hear your thoughts, feedbacks and experiences about this library. With this, we can help each other to further improve it.*

#### The benefits of using the Bulk Operations

**Nothing else, but PERFORMANCE!**

Although, the same exact goal can be achieved by Batch Operations (i.e.: [DeleteAll](/operation/deleteall), [InsertAll](/operation/insertall), [MergeAll](/operation/mergeall) and [UpdateAll](/operation/updateall)), but, by leveraging the real Bulk Operations functionalities, you are extremely improving the performance of your data manipulation with a huge-percentage gap.

To explain further, when you only use the Batch operations, it is only batching the multiple atomic activities and still does the multiple round-trips from your application into the database server. However, when you use the Bulk operations, you are bringing all the data from the application into the database server at once and at the same time ignoring some database specific activities (i.e.: `Logging`, `Audits`, `Data-Type Checks`, `Constraints`, etc) behind the scene, thus gives you maximum performance during the operation.

#### How does it works?

It is leveraging the ADO.NET `SqlBulkCopy` class (both `System.Data.SqlClient/Microsoft.Data.SqlClient`).

For [BulkInsert](/operation/bulkinsert) operation, it simply calls the *WriteToServer* method to bring all the data from the application into the database. No additional logic is implied.

For [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations, an implied logic and technique has been utilized.

Basically, a pseudo-temporary table will be created in the database under the transaction context. It then re-uses the [BulkInsert](/operation/bulkinsert) operation to target that pseudo-temporary table. Through this technique, we brought all the data together from the client application into the database server (at one-go).

You as a developer has the opportunity to maximize your own `qualifiers` by passing it as a list of [Field](/class/field) objects. The library will then create a `CLUSTERED INDEX` on the pseudo-temporary table using your qualifiers.

> If you have not passed any qualifiers, the primary key will be used by default. If the primary key is not present, it will use the identity field instead.

Once all the data is in the database pseudo-temporary table, the correct SQL Statement will be used to cascade the changes towards the original table.

See the SQL Statements below.

###### For BulkDelete

```csharp
> DELETE T
> FROM [dbo].[OriginalTable] T
> INNER JOIN [PseudoTempTable] TMP ON TMP.QualiferField1 = T.Field1 AND TMP.QualifierField2 = T.Field2;
```

###### For BulkMerge

```csharp
> MERGE [dbo].[OriginalTable] T
> USING [PseudoTempTable] S ON S.QualiferField1 = T.Field1 AND S.QualifierField2 = T.Field2
> WHEN NOT MATCHED THEN
>      INSERT (...) VALUES (...)
> WHEN MATCHED THEN
>     UPDATE
>     SET (...);
```

###### For BulkUpdate

```csharp
> UPDATE T
> SET T.Field3 = TMP.Field3
>     , T.Field4 = TMP.Field4
>     , ...
> FROM [OriginalTable] T
> INNER JOIN [PseudoTempTable] TMP ON TMP.QualiferField1 = T.Field1 AND TMP.QualifierField2 = T.Field2;
```

###### The following objects are supported

- `System.DataTable`
- `System.Data.Common.DbDataReader`
- `System.Collections.Generic.IEnumerable<T>`

## Special Arguments

The arguments `qualifiers` and `usePhysicalPseudoTempTable` is provided at [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations.

The argument `qualifiers` is used to define the qualifier fields to be used in the operation. It usually refers to the *WHERE* expression of SQL Statements. If not given, the primary or identity column will be used.

The argument `usePhysicalPseudoTempTable` is used to define whether a physical pseudo-table will be created during the operation. By default, a temporary table (i.e.: #TableName) is used.

## Caveats

RepoDB is automatically setting the value of options argument to `SqlBulkCopyOptions.KeepIdentity` when calling the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) if you have not passed any `qualifiers` and if your table has an IDENTITY primary key column. The same logic will apply if there is no primary key but has an IDENTITY column defined in the table.

In addition, when calling the [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) operations, the library is creating a pseudo temporary table behind the scene. It requires your user to have the correct privilege to `CREATE` a table in the database, otherwise a `SqlException` will be thrown.

## Get Started

To start with, simply install the library. At the Package Manager Console, type the command below.

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

> You should install the version 1.0.4 (or above). Prior versions only support BulkInsert.

Then, call the boostrapper once.

```csharp
SqlServerBootstrap.Initialize();
```

After that, you can then start calling the Bulk Operations. See the sample code snippets below.

### BulkDelete

The code snippets below only showcasing the [BulkDelete](/operation/bulkdelete) via `System.Collections.Generic.IEnumerable<T>`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkDelete<Customer>(customers);
}
```

Or with qualifiers

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var qualifiers = Field.From("LastName", "BirthDate");
	var rows = connection.BulkDelete<Customer>(customers, qualifiers: qualifiers);
}
```

Or with primary keys

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var primaryKeys = new [] { 10045, ..., 11011 };
	var rows = connection.BulkDelete<Customer>(primaryKeys);
}
```

Or via table-name

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkDelete("Customer", customers);
}
```

Or via table-name with qualifiers

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var qualifiers = Field.From("LastName", "BirthDate");
	var rows = connection.BulkDelete("Customer", customers, qualifiers: qualifiers);
}
```

### BulkInsert

The code snippets below only showcasing the [BulkInsert](/operation/bulkinsert) via `System.Collections.Generic.IEnumerable<T>`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkInsert<Customer>(customers);
}
```

Or via table-name

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkInsert("Customer", customers);
}
```

### BulkMerge

The code snippets below only showcasing the [BulkMerge](/operation/bulkmerge) via `System.Collections.Generic.IEnumerable<T>`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkMerge<Customer>(customers);
}
```

Or with qualifiers

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var qualifiers = Field.From("LastName", "BirthDate");
	var rows = connection.BulkMerge<Customer>(customers, qualifiers: qualifiers);
}
```

Or via table-name

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkMerge("Customer", customers);
}
```

Or via table-name with qualifiers

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var qualifiers = Field.From("LastName", "BirthDate");
	var rows = connection.BulkMerge("Customer", customers, qualifiers: qualifiers);
}
```

### BulkUpdate

The code snippets below only showcasing the [BulkUpdate](/operation/bulkupdate) via `System.Collections.Generic.IEnumerable<T>`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkUpdate<Customer>(customers);
}
```

Or with qualifiers

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var qualifiers = Field.From("LastName", "BirthDate");
	var rows = connection.BulkUpdate<Customer>(customers, qualifiers: qualifiers);
}
```

Or via table-name

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var rows = connection.BulkUpdate("Customer", customers);
}
```

Or via table-name with qualifiers

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = GetCustomers();
	var qualifiers = Field.From("LastName", "BirthDate");
	var rows = connection.BulkUpdate("Customer", customers, qualifiers: qualifiers);
}
```

> As mentioned in the early section, both the `System.Data.Common.DbDataReader` and `System.DataTable` is supported.

## When to use the Batch and Bulk Operations?

Here, we can only advice based on our experience. To be honest, there is no standard of when to use what. It all depends on your situation (i.e.: Network Latency, Data, No of Columns, etc).

The PROS of using Bulk is maximum performance. However, there are also CONS of usually using it. One of it is it keeps blocking the target table while being under the Bulk operations transaction. It can however trigger (or also solve) a deadlock if not handled properly by the developers.

In our end, we usually use the Batch operations if the rows we are manipulating is less than or equal `1000`. Beyond than that, we standardized to use the Bulk operations.

## Summary

By using the [RepoDb.SqlServer.BulkOperations](https://github.com/mikependon/RepoDb/tree/master/RepoDb.Extensions/RepoDb.SqlServer.BulkOperations) features, you will have the following.

- Maximizing the performance of your application when processing the large datasets.
- No need to write complex implementation of the BulkOperations. Every call is a single-line of code.
- Handled in OSS community, reviewed and used by the others.
- Packed with high-quality.
- Simple implementation. The code snippets are very easy to operate and maintain.

----------

The original post was posted at [DEV.to](https://dev.to/mikependon/c-bulk-operations-for-net-core-bulkdelete-bulkinsert-bulkmerge-bulkupdate-is-now-very-simple-with-repodb-orm-3can) by the author.

Thank you for reading this article.