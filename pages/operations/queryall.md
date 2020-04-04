---
layout: page
title: "QueryAll (RepoDb)"
permalink: /operation/queryall
tags: [repodb, tutorial, queryall, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## QueryAll

This method is used to fetch all the rows from the database.

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

Below is a sample code to fetch a row from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.QueryAll<Person>();
}
```

#### Targetting a Table

You can also target a specific table by passing the literal table like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.QueryAll("[dbo].[Person]");
}
```

> The result is a list of dynamic objects of type `ExpandoObject`.

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.QueryAll<Person>(hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.QueryAll<Person>(hints: SqlServerTableHints.TabLock);
}
```

#### Ordering the Results

To order the results, you have to pass an array of `OrderField` objects in the `orderBy` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var orderBy = OrderField.Parse(new
	{
		LastName = Order.Descending,
		FirstName = Order.Ascending
	});
	var people = connection.QueryAll<Person>(orderBy: orderBy);
	// Do the stuffs for 'people' here
}
```

#### Filtering the Results

To filter the results, you have to pass a value at the `top` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.QueryAll<Person>(top: 100);
	// Do the stuffs for 'people' here
}
```

#### Caching the Results

To cache the results, simply pass a literal string key into the `cacheKey` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.QueryAll<Person>(cacheKey: "CackeKey:AllPeople");
	// Do the stuffs for 'people' here
}
```

> The cache expiration is defaulted to `180` minutes. You can override it by passing an integer value at the `cacheExpirationInMinutes` argument.
