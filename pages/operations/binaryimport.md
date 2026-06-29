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

This method inserts multiple rows into the database in bulk. It is supported only for [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations).

## Call Flow Diagram

The diagram below shows the flow when calling this operation.

<img src="../../assets/images/site/binaryimport.svg" />

## Use Case

Use this method to insert rows at high speed. It leverages the native bulk operation from the Npgsql library via the [NpgsqlBinaryImporter](https://www.npgsql.org/doc/api/Npgsql.NpgsqlBinaryImporter.html) class.

For inserting 1,000 or more rows, prefer this method over [InsertAll](/operation/insertall). The [BinaryBulkInsert](/operation/binarybulkinsert) operation is also available as an alternative.

## Special Arguments

The `keepIdentity` argument controls whether the identity property of the entity/model is preserved during the operation.

## Usability

Pass the list of entities to the operation.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var importedRows = connection.BinaryImport<Person>(people);
}
```

{: .note }
> It returns the number of rows inserted into the underlying table.

To specify a batch size:

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var importedRows = connection.BinaryImport<Person>(people, batchSize: 100);
}
```

{: .important }
> If `batchSize` is not set, all items in the collection are sent at once.

To target a specific table, pass the literal table name.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var importedRows = connection.BinaryImport("[dbo].[Person]", people);
}
```

#### DataTable

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var table = ConvertToDataTable(people);
    var importedRows = connection.BinaryImport("[dbo].[Person]", table);
}
```

#### Dictionary/ExpandoObject

```csharp
var people = GetPeopleAsDictionary(1000);

using (var connection = new NpgsqlConnection(destinationConnectionString))
{
    var importedRows = connection.BinaryImport("[dbo].[Person]", people);
}
```

#### DataReader

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

Or via [DataEntityDataReader](/class/dataentitydatareader).

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var importedRows = connection.BinaryImport("[dbo].[Person]", reader);
    }
}
```
