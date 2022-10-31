---
layout: default
sidebar: operations
title: "BulkMerge"
permalink: /operation/bulkmerge
tags: [repodb, tutorial, bulkmerge, orm, hybrid-orm, sqlserver]
parent: OPERATIONS
---

# BulkMerge

---

This method is used to merge all the rows from the client application into the database by bulk. It is only supporting the [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations) RDBMS.

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

<img src="../../assets/images/site/bulkmerge.svg" />

## Use Case

This method is very useful if you are merging multiple rows towards the database in a very speedy manner. It is high-performant in nature as it is using the real bulk operation natively from ADO.NET (via `SqlBulkCopy` class).

If you are working to merge range of rows from 1000 or more, then use this method over the [MergeAll](/operation/mergeall) operation.

## Special Arguments

The arguments `qualifiers`, `isReturnIdentity`, and `usePhysicalPseudoTempTable` is provided on this operation.

The `qualifiers` is used to define the qualifier fields to be used in the operation. It usually refers to the WHERE expression of the SQL Statement. If not given, the primary column will be used.

The `isReturnIdentity` is used to define the behaviour of the execution whether the newly generated identities will be set-back to the data entities. By default, it is disabled.

The `usePhysicalPseudoTempTable` is used to define whether a physical pseudo-table will be created during the operation. By default, a temporary table (i.e.: `#TableName`) is used.

{: .important }
> It is not recommended to enable the `usePhysicalPseudoTempTable` argument if you are to work with parallelism. Ensure to always utilize the session-based non-physical pseudo-temporary table when working with parallelism.

## Identity Setting Alignment

The library has enforced an additional logic to ensure the identity setting alignment if the `isReturnIdentity` is enabled during the calls.

Basically, a new column named `__RepoDb_OrderColumn` is being added into the pseudo-temporary table if the identity field is present on the underlying target table. This column will contain the actual index of the entity model from the `IEnumerable<T>` object.

During the bulk operation, a dedicated `DbParameter` object is created that targets this additional column with a value of the entity model index, thus ensuring that the index value is really equating the index of the entity data from the `IEnumerable<T>` object. The resultsets of the pseudo-temporary table are being ordered using this newly generated column prior the actual merge to the underlying table.

When the newly generated identity value is being set back to the data model, the value of the `__RepoDb_OrderColumn` column is being used to look-up the proper index of the equating entity model from the `IEnumerable<T>` object, then, the compiled identity-setter function is used to assign back the identity value into the identity property.

## Caveats

RepoDB is automatically setting the value of the `options` argument to `SqlBulkCopyOptions.KeepIdentity` when calling this method and if you have not passed any qualifiers and if your table has an IDENTITY primary key column. The same logic will apply if there is no primary key but has an IDENTITY column defined in the table.

In addition, when calling this method, the library is creating a pseudo temporary table behind the scene. It requires your user to have the correct privilege to create a table in the database, otherwise a `SqlException` will be thrown.

## Usability

Let us say you have a list of `Person` model at variable `people` that contains both the existing and non-existing rows from the database.

Then, below is the code to bulk-merge those rows `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people);
}
```

And below if you would like to specify the batch size.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var mergedRows = connection.BulkMerge(people, batchSize: 100);
}
```

{: .note }
> By default, the batch size is 10, equals to `Constant.DefaultBatchOperationSize` value.

#### DataTable

Below is the sample code to bulk-merge via data table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var table = ConvertToDataTable(people);
    var mergedRows = connection.BulkMerge<Person>(table);
}
```

#### Dictionary/ExpandoObject

Below is the sample code to bulk-merge via `Dictionary<string, object>` or `ExpandoObject`.

```csharp
using (var sourceConnection = new SqlConnection(sourceConnectionString))
{
    var result = sourceConnection.QueryAll("Person");
    using (var destinationConnection = new SqlConnection(destinationConnectionString))
    {
        var mergedRows = destinationConnection.BulkMerge("Person", result,
            qualifiers: Field.From("LastName", "DateOfBirth"));
    }
}
```

#### DataReader

Below is the sample code to bulk-merge via `DbDataReader`.

```csharp
using (var sourceConnection = new SqlConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE (IsActive = 1);"))
    {
        using (var destinationConnection = new SqlConnection(destinationConnectionString))
        {
            var rows = destinationConnection.BulkMerge<Person>(reader);
        }
    }
}
```

Or you can also convert the list of `Person` into a `DataEntityDataReader` object and bulk-merge it.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var mergedRows = connection.BulkMerge<Person>(reader);
    }
}
```

## Field Qualifiers

By default, this method is using the primary column as the qualifier. You can override it by passing the list of [Field](/class/field) objects in the `qualifiers` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge<Person>(people,
        qualifiers: new { e.LastName, e.DateOfBirth });
}
```

{: .important }
> When using the qualifiers, we recommend that you use the list of columns that has the correct index from the original table.

## Column Mappings

You can add a mapping via `BulkInsertMapItem` class.

```csharp
var mappings = new List<BulkInsertMapItem>();

// Add the mappings
mappings.Add(new BulkInsertMapItem("SourceId", "DestinationId"));
mappings.Add(new BulkInsertMapItem("SourceName", "DestinationName"));
mappings.Add(new BulkInsertMapItem("SourceIsActive", "DestinationIsActive"));
mappings.Add(new BulkInsertMapItem("SourceDateInsertedUtc", "DestinationDateInsertedUtc"));

// Execute
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        mappings: mappings);
}
```

## Bulk Copy Options

You can define your bulk-copy options by passing a value of `SqlBulkCopyOptions` in the `options` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        options: SqlBulkCopyOptions.KeepIdentity);
}
```

## Targeting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge("[dbo].[Person]", people);
}
```

## Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge(people,
        hints: SqlServerTableHints.TabLock);
}
```
