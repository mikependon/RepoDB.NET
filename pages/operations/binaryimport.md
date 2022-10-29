---
layout: default
sidebar: operations
title: "BinaryImport"
permalink: /operation/binaryimport
tags: [repodb, tutorial, binaryimport, orm, hybrid-orm, sqlserver]
parent: OPERATIONS
---

# BinaryImport

---

This method is used to insert multiple rows towards the database by bulk. It is only supporting the [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

<img src="../../assets/images/site/binaryimport.svg" />

## Use Case

This method is very useful if you would like to insert multiple rows towards the database in a very speedy manner. It is high-performant in nature as it is using the real bulk operation natively from the Npgsql library (via the [NpgsqlBinaryImporter](https://www.npgsql.org/doc/api/Npgsql.NpgsqlBinaryImporter.html) class).

If you are working to insert range of rows from 1000 or more, then use this method over the [InsertAll](/operation/insertall) operation. Alternatively, you can also use the [BinaryBulkInsert](/operation/binarybulkinsert) operation.

## Special Arguments

The `keepIdentity` argument is provided to define a value whether the identity property of the entity/model will be kept during the operation. 

## Usability

Simply pass the list of the entities when calling this operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var importedRows = connection.BinaryImport<Person>(people);
}
```

> It returns the number of rows inserted into the underlying table.

And below if you would like to specify the batch size.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var importedRows = connection.BinaryImport<Person>(people, batchSize: 100);
}
```

> If the `batchSize` argument is not set, then all the items from the collection will be sent together.

You can also target a specific table by passing the literal table name like below.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var importedRows = connection.BinaryImport("[dbo].[Person]", people);
}
```

#### DataTable

Below is the sample code to bulk-insert via data table.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var table = ConvertToDataTable(people);
    var importedRows = connection.BinaryImport("[dbo].[Person]", table);
}
```

#### Dictionary/ExpandoObject

Below is the sample code to bulk-insert via `Dictionary<string, object>` or `ExpandoObject`.

```csharp
var people = GetPeopleAsDictionary(1000);

using (var connection = new NpgsqlConnection(destinationConnectionString))
{
    var importedRows = connection.BinaryImport("[dbo].[Person]", people);
}
```

#### DataReader

Below is the sample code to bulk-insert via `DbDataReader`.

```csharp
using (var sourceConnection = new NpgsqlConnection(sourceConnectionString))
{
    using (var reader = sourceConnection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        using (var destinationConnection = new NpgsqlConnection(destinationConnectionString))
        {
            var importedRows = destinationConnection.BinaryImport("[dbo].[Person]", reader);
        }
    }
}
```

Or via [DataEntityDataReader](/class/dataentitydatareader) class.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var importedRows = connection.BinaryImport("[dbo].[Person]", reader);
    }
}
```