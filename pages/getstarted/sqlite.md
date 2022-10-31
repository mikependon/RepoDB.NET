---
layout: default
sidebar: getstarted
title: "SQLite"
description: "Learn on how to work with SQLite databases using RepoDB library."
nav_order: 3
permalink: /tutorial/get-started-sqlite
tags: [repodb, tutorial, get-started, orm, hybrid-orm, sqlite]
parent: GET STARTED
---

# Get Started for SQLite

---

RepoDB is a hybrid .NET ORM library for SQLite RDBMS. The project is hosted at Github and is licensed with [Apache 2.0](http://apache.org/licenses/LICENSE-2.0.html).

## Installation

The library can be installed via Nuget. In your Package Manager Console, type the command below.

### System.Data.SQLite.Core

```csharp
> Install-Package RepoDb.SQLite.System
```

Once installed, call the globalized setup method to initialize all the dependencies for SQLiteConnection.

```csharp
GlobalConfiguration
	.Setup()
	.UseSQLite();
```

For the users prior the version 1.13.0, use the bootstrapper code below.

```csharp
RepoDb.SQLiteBootstrap.Initialize();
```

### Microsoft.Data.Sqlite

```csharp
> Install-Package RepoDb.Sqlite.Microsoft
```

Once installed, call the globalized setup method to initialize all the dependencies for SqliteConnection.

```csharp
GlobalConfiguration
	.Setup()
	.UseSqlite();
```

For the users prior the version 1.13.0, use the bootstrapper code below.

```csharp
RepoDb.SqliteBootstrap.Initialize();
```

Or, visit our [installation](/tutorial/installation) page for more information.

## Create a Table

Let us say you have this table on your database.

```csharp
CREATE TABLE IF NOT EXISTS [Person]
(
    Id INTEGER PRIMARY KEY AUTOINCREMENT,
    Name TEXT,
    Age INTEGER,
    CreatedDateUtc DATETIME
);
```

## Create a Model

And you have this model on your application.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

## Creating a Record

To create a row, use the [Insert](/operation/insert) method.

```csharp
var person = new Person
{
    Name = "John Doe",
    Age = 54,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SQLiteConnection(ConnectionString))
{
    var id = connection.Insert(person);
}
```

To insert multiple rows, use the [InsertAll](/operation/insertall) operation.

```csharp
var people = GetPeople(100);
using (var connection = new SQLiteConnection(ConnectionString))
{
    var rowsInserted = connection.InsertAll(people);
}
```

{: .note }
> The [Insert](/operation/insert) method returns the value of primary column, while the [InsertAll](/operation/insertall) method returns the number of rows inserted. Both methods are automatically setting back the value of the primary property of the model if present.

## Querying a Record

To query a row, use the [Query](/operation/query) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var person = connection.Query<Person>(e => e.Id == 1);
    /* Do the stuffs for the 'person' here */
}
```

To query all the rows, use the [QueryAll](/operation/queryall) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var people = connection.QueryAll<Person>();
    /* Do the stuffs for the 'people' here */
}
```

## Merging a Record

To merge a row, use the [Merge](/operation/merge) method.

```csharp
var person = new Person
{
    Id = 1,
    Name = "John Doe",
    Age = 57,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SQLiteConnection(ConnectionString))
{
    var id = connection.Merge(person);
}
```

By default, the primary column is used as a qualifier. You can also customize the qualifiers with other columns.

```csharp
var person = new Person
{
    Name = "John Doe",
    Age = 57,
    CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new SQLiteConnection(ConnectionString))
{
    var id = connection.Merge(person, qualifiers: (p => new { p.Name }));
}
```

To merge all the rows, use the [MergeAll](/operation/mergeall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new SQLiteConnection(ConnectionString))
{
    var affectedRecords = connection.MergeAll<Person>(people);
}
```

{: .note }
> The [Merge](/operation/merge) method returns the primary column value, while the [MergeAll](/operation/mergeall) method returns the number of rows affected. Both methods are automatically setting back the value of the primary property if present.

## Deleting a Record

To delete a row, use the [Delete](/operation/delete) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(1);
}
```

By default, the primary column is used as a qualifier, but you can also use the other columns like below.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all the rows, use the [DeleteAll](/operation/deleteall) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

You can also pass the list of primary keys to be deleted.

```csharp
using (var connection = new SQLiteConnection(connectionString))
{
    var primaryKeys = new [] { 10045, 11001, .., 12011 };
    var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

{: .note }
> Both the [Delete](/operation/delete) and [DeleteAll](/operation/deleteall) methods return the number of rows affected during the execution.

## Updating a Record

To update a row, use the [Update](/operation/update) method.

```csharp
var person = new Person
{
    Id = 1,
    Name = "James Doe",
    Age = 55,
    DateInsertedUtc = DateTime.UtcNow
};
using (var connection = new SQLiteConnection(ConnectionString))
{
    var updatedRows = connection.Update<Person>(person);
}
```

You can also update via dynamic by targeting certain columns.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var updatedRows = connection.Update("Person", new { Id = 1, Name = "James Doe" });
}
```

To update all the rows, use the [UpdateAll](/operation/updateall) method.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SQLiteConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people);
}
```

By default, the primary column is used as a qualifier, but you can also specify your custom qualifiers.

```csharp
var people = GetPeople(100);
people
    .AsList()
    .ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new SQLiteConnection(ConnectionString))
{
    var updatedRows = connection.UpdateAll<Person>(people,
        qualifiers: (p => new { p.Name }));
}
```

{: .note }
> Both the [Update](/operation/update) and [UpdateAll](/operation/updateall) methods return the number of rows affected during the execution.

## Executing a Query

To execute a query, use the [ExecuteNonQuery](/operation/executenonquery) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var sql = "DELETE FROM [Person] WHERE Id = @Id;";
    var affectedRecords = connection.ExecuteNonQuery(sql, new { Id = 1 });
}
```

To execute a query while expecting a result of class object, use the [ExecuteQuery](/operation/executequery) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var sql = "SELECT * FROM [Person] ORDER BY Id ASC;";
    var people = connection.ExecuteQuery<Person>(sql);
    /* Do the stuffs for the people here */
}
```

To execute a query while expecting a single result, use the [ExecuteScalar](/operation/executescalar) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var sql = "SELECT MAX(Id) FROM [Person];";
    var maxId = connection.ExecuteQuery<Person>(sql);
}
```

To execute a query while expecting a result of `DbDataReader` object, use the [ExecuteReader](/operation/executereader) method.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var sql = "SELECT * FROM [Person] ORDER BY Id ASC;"
    using (var reader = connection.ExecuteReader(sql))
    {
        /* Do the stuffs for the data reader here */
    }
}
```

## Typed Result Execution

You can infer the single-column resultsets to any .NET CLR type via [ExecuteQuery](/operation/executequery) operation.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var sql = "SELECT Name FROM [Person] WHERE Id = @Id;";
    var name = connection.ExecuteQuery<string>(sql, new { Id = 10045 });
}
```

And even with the enumerations.

```csharp
public enum Gender
{
    Male,
    Female
}
```

Then, call it like below.

```csharp
using (var connection = new SQLiteConnection(ConnectionString))
{
    var sql = "SELECT Gender FROM [Person] WHERE Id = @Id;";
    var name = connection.ExecuteQuery<Gender>(sql, new { Id = 10045 });
}
```

{: .note }
> The resultset of this operation is an `IEnumerable<T>` object.
