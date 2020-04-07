---
layout: navpage
sidebar: operations
title: "Delete (RepoDb)"
permalink: /operation/delete
tags: [repodb, tutorial, delete, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Delete

This method is used to delete a record from the database.

#### Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

#### Installation

To install, simply type the codes below in your Package Manager Console.

```csharp
> Install-Package RepoDb.SqlServer
```

Then call the bootstrapper once.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

Or visit our [installation](/tutorial/installation) page for more information.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Learnings

Below is a sample code to delete a row from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.Delete<Person>(person);
}
```

You can also delete directly by primary key.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.Delete<Person>(10045);
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.Delete("[dbo].[Person]", person);
}
```

Or, via primary key.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.Delete("[dbo].[Person]", 10045);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.Delete<Person>(person,
		hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var deletedRows = connection.Delete<Person>(person,
		hints: SqlServerTableHints.TabLock);
}
```
