---
layout: post
title: "RepoDB - a hybrid ORM library for .NET"
author: "Michael Camara Pendon"
date: 2020-04-02 12:00:00 +0100
categories: blogs repodb
---

### Introduction

Introducing RepoDB, a new high performant and efficient hybrid ORM library for .NET.

To begin with, you can start reading our [Get Started](/tutorial/get-started-sqlserver) page or learn more from our [documentation](/docs).

### Why use RepoDB?

- Easy installation, only takes few seconds.
- No controlled layer like `DbContext`, those make the developers speed-up the usability.
- Calls to fluent and raw-SQL methods is just a dot away.
- `Repository` implementation becomes more simpler by leveraging the built-in repositories.
- Can work without the models; everything can be `dynamics`.
- Ease of pain when working with large data sets.
- Minimizes the round trips with `2nd-Layer` cache.
- Transmission of data from different RDBMS DB Providers only take few lines of codes.

### How does it works?

Basically, all [operations](/docs#operations) were implemented as extended methods of the [IDbConnection](https://learn.microsoft.com/en-us/dotnet/api/system.data.idbconnection?view=net-7.0) object. Once you hold the opened-state of your database connection object, you can then do all the activities you would like to do with your database through those extended methods.

#### See sample code snippets below:

For [Query](/operation/query).

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customer= connection.Query<Customer>(10045).FirstOrDefault();
}
```

For [Insert](/operation/insert).

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customer = new Customer
	{
		Name = "John Doe",
		Address = "New York"
	};
	var id = connection.Insert<Customer>(customer);
}
```

For [Update](/operation/insert).

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customer = new Customer
	{
		Id = 10045,
		Name = "Jane Doe",
		Address = "Chicago"
	};
	var rowsAffected = connection.Update<Customer>(customer);
}
```

And for [Delete](/operation/delete).

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.Delete<Customer>(10045);
}
```

You can also execute a raw SQLs via `Execute` methods (i.e.: [ExecuteQuery](/operation/executequery), [ExecuteNonQuery](/operation/executenonquery), [ExecuteScalar](/operation/executescalar) and [ExecuteReader](/operation/executereader)).

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = connection.ExecuteQuery<Customer>("SELECT * FROM [dbo].[Customer];");
}
```

Or even executing a stored procedure.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var customers = connection.ExecuteQuery<Customer>("sp_GetCustomersByState", new { State = "New York" }, commandType: CommandType.StoredProcedure);
}
```

### Core Features

Below are the list of features available.

- [Batch Operations](/feature/batchoperations)
- [Bulk Operations](/feature/bulkoperations)
- [Caching](/feature/caching)
- [Connection Persistency](/feature/connectionpersistency)
- [Enumeration](/feature/enumeration)
- [Expression Trees](/feature/expressiontrees)
- [Field/Class Mapping](/feature/fieldclassmapping)
- [Hints](/feature/hints)
- [Multiple Query](/feature/multiplequery)
- [Property Handlers](/feature/propertyhandlers)
- [Repositories](/feature/repositories)
- [Tracing](/feature/tracing)
- [Transaction](/feature/transaction)
- [Type Mapping](/feature/typemapping)

To learn more about these [operation](/docs#operations), please visit our [documentation](/docs) page.

### Database Supports

The library supports the 
You can use the library to work with [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer), [SQLite](https://www.nuget.org/packages/RepoDb.SqLite), [MySQL](https://www.nuget.org/packages/RepoDb.MySql) and [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql) Relational Database Management Systems (RDBMS).

### Quality

It also covers a lot of real-world scenarios through its rich [Unit Tests](https://github.com/mikependon/RepoDb/tree/master/RepoDb.Core/RepoDb.Tests/RepoDb.UnitTests) and [Integration Tests](https://github.com/mikependon/RepoDb/tree/master/RepoDb.Core/RepoDb.Tests/RepoDb.IntegrationTests). 

### Feedback

Please let us know about your feedback if you have some.

Lastly, please do not forget to share this to your friends and colleagues; give us your valuable STAR on our [GitHub](https://github.com/mikependon/RepoDb) repository.

-----------

We are here to engage more with the .NET Community and collaborate further for the development of this library.