---
layout: post
title: "Empowering MySQL DB Access with MySqlConnector + RepoDB"
author: "Michael Camara Pendon"
date: 2020-05-30 5:55:00 +0200
categories: blogs repodb
---

# Empowering MySQL DB Access with MySqlConnector + RepoDB

---

### Introduction

[MySqlConnector](https://mysqlconnector.net/) is an ADO.NET data provider for MySQL Server, MariaDB, Percona Server, Amazon Aurora, Azure Database for MySQL, Google Cloud SQL for MySQL and more. It provides implementations of DbConnection, DbCommand, DbDataReader, DbTransaction — the classes needed to query and update databases from managed code. MySqlConnector claims to be the real asynchronous data provider for MySQL in .NET space. It is known to much more faster and optimal than [MySql.Data](https://www.nuget.org/packages/MySql.Data/) data provider the Oracle has written.

[RepoDB](http://repodb.net/) is an open-source .NET ORM that bridge the gaps between micro-ORMs and full-ORMs. It helps the developer to simplify the switch-over of when to use the BASIC and ADVANCE operations during the development. It is your best alternative ORM to both Dapper and Entity Framework. RepoDB claims to be the most-fastest and most-efficient ORM library in .NET in which even surpassed the [Dapper](https://www.nuget.org/packages/Dapper/) performance by huge gap. It is best suited for the developers who wish to control the Data Access Layers with a more simple and dynamic implementations.

We are knocking on the windows of .NET/MySQL community about this newly built and released capability for MySQL DB accessibility space. With the combination of these 2 libraries, the DB accessibility for MySQL is now very powerful as both packages are offering different advantages for its users.

### About the Library

The library is deployed as Nuget Package named [RepoDb.MySqlConnector](https://www.nuget.org/packages/RepoDb.MySqlConnector) in which both being authored by the authors of RepoDB and MySqlConnector. The project is hosted at [Github](https://github.com/mikependon/RepoDb/tree/master/RepoDb.MySqlConnector) as extension of RepoDb.

The library is currently on its first version release as of writing this. The test suites (both [Unit](https://github.com/mikependon/RepoDb/tree/master/RepoDb.MySqlConnector/RepoDb.MySqlConnector.UnitTests) and [Integration](https://github.com/mikependon/RepoDb/tree/master/RepoDb.MySqlConnector/RepoDb.MySqlConnector.IntegrationTests) tests) were written to cover hundred of real-life business scenarios for all its operations.

By using this library, you had inherited all the capabilities of both powerful packages in one installation.

### Get Started

To get started, install the package via Package Manager Console.

```csharp
> Install-Package RepoDb.MySqlConnector
```

Then, call the bootstrapper once.

```csharp
RepoDb.MySqlBootstrap.Initialize();
```

Once the bootstrapping is done, then the library can then be used right away.

Or, please visit our official MySQL [Get Started](http://repodb.net/tutorial/get-started-mysql) page for more information.

#### Inserting a Record

To insert a record, use the [Insert](http://repodb.net/operation/insert) method.

```csharp
var person = new Person
{
	Name = "John Doe",
	Age = 54,
	CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new MySqlConnection(ConnectionString))
{
	var id = connection.Insert(person);
}
```

To insert multiple records, use the [InsertAll](http://repodb.net/operation/insertall) method.

```csharp
var people = GetPeople(100);
using (var connection = new MySqlConnection(ConnectionString))
{
	var rowsInserted = connection.InsertAll(people);
}
```

#### Querying a Record

To query a record, use the [Query](http://repodb.net/operation/query) method.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	var person = connection.Query<Person>(e => e.Id == 1);
	/* Do the stuffs for the 'person' here */
}
```

To query all the records, use the [QueryAll](http://repodb.net/operation/queryall) method.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	var people = connection.QueryAll<Person>();
	/* Do the stuffs for the 'people' here */
}
```

#### Merging a Record

To merge a record, use the [Merge](http://repodb.net/operation/merge) method.

```csharp
var person = new Person
{
	Id = 1,
	Name = "John Doe",
	Age = 57,
	CreatedDateUtc = DateTime.UtcNow
};
using (var connection = new MySqlConnection(ConnectionString))
{
	var id = connection.Merge(person);
}
```

To merge all the rows, use the [MergeAll](http://repodb.net/operation/mergeall) method.

```csharp
var people = GetPeople(100);
people
	.AsList()
	.ForEach(p => p.Name = $"{p.Name} (Merged)");
using (var connection = new MySqlConnection(ConnectionString))
{
	var affectedRecords = connection.MergeAll<Person>(people);
}
```

#### Deleting a Record

To delete a record, use the [Delete](http://repodb.net/operation/delete) method.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	var deletedRows = connection.Delete<Person>(1);
}
```

By default, it uses the primary key as a qualifier. The other fields can also be used via Linq expression.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	var deletedRows = connection.Delete<Person>(p => p.Name == "John Doe");
}
```

To delete all the rows, use the [DeleteAll](http://repodb.net/operation/deleteall) method.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	var deletedRows = connection.DeleteAll<Person>();
}
```

The list of primary keys can also be passed for this method.

```csharp
using (var connection = new MySqlConnection(connectionString))
{
	var primaryKeys = new [] { 10045, 11001, ..., 12011 };
	var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

#### Updating a Record

To update a record, use the [Update](http://repodb.net/operation/update) method.

```csharp
var person = new Person
{
	Id = 1,
	Name = "James Doe",
	Age = 55,
	DateInsertedUtc = DateTime.UtcNow
};
using (var connection = new MySqlConnection(ConnectionString))
{
	var updatedRows = connection.Update<Person>(person);
}
```

To update all the rows, use the [UpdateAll](http://repodb.net/operation/updateall) method.

```csharp
var people = GetPeople(100);
people
	.AsList()
	.ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new MySqlConnection(ConnectionString))
{
	var updatedRows = connection.UpdateAll<Person>(people);
}
```

By default, the primary or identity column is used as a qualifier. The other fields can also be used as the qualifiers.

```csharp
var people = GetPeople(100);
people
	.AsList()
	.ForEach(p => p.Name = $"{p.Name} (Updated)");
using (var connection = new MySqlConnection(ConnectionString))
{
	var updatedRows = connection.UpdateAll<Person>(people, qualifiers: Field.From("Name", "DateOfBirth"));
}
```

#### Executing a Query

To execute a query, use the [ExecuteNonQuery](http://repodb.net/operation/executenonquery) method.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	var affectedRecords = connection.ExecuteNonQuery("DELETE FROM `Person` WHERE Id = @Id;", new { Id = 1 });
}
```

To execute a query while expecting a result of class object, use the [ExecuteQuery](http://repodb.net/operation/executequery) method.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	var people = connection.ExecuteQuery<Person>("SELECT * FROM `Person` ORDER BY Id ASC;");
	/* Do the stuffs for the 'people' here */
}
```

To execute a query while expecting a single result, use the [ExecuteScalar](http://repodb.net/operation/executescalar) method.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	var maxId = connection.ExecuteQuery<Person>("SELECT MAX(Id) FROM `Person`;");
}
```

To execute a query while expecting a result of `DbDataReader`, use the [ExecuteReader](http://repodb.net/operation/executereader) method.

```csharp
using (var connection = new MySqlConnection(ConnectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM `Person` ORDER BY Id ASC;"))
	{
		/* Do the stuffs for the data reader here */
	}
}
```

### Community Address

We are here to gather some feedback from the community pertaining to the experiences when using the library.

For any issues, you can directly visits the official channels of RepoDb.

- [GitHub](https://github.com/mikependon/RepoDb/issues) - for any issues, requests and problems.
- [StackOverflow](https://stackoverflow.com/questions/tagged/repodb) - for any technical questions.
- [Twitter](https://twitter.com/search?q=%23repodb) - for the latest news.
- [Gitter Chat](https://gitter.im/RepoDb/community) - for direct and live Q&A.

Thank you for reading this article.