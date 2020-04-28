---
layout: navpage
sidebar: operations
title: "Query"
permalink: /operation/query
tags: [repodb, tutorial, query, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Query

This method is used to fetch the rows from the database.

#### Data Providers

Below are the supported data providers by this operation.

- [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer)
- [SQLite](https://www.nuget.org/packages/RepoDb.SqLite)
- [MySQL](https://www.nuget.org/packages/RepoDb.MySql)
- [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql)

#### Installation

To install, simply type the code snippets below in your Package Manager Console.

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
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.Query<Person>(10045).FirstOrDefault();
}
```

You can also query via expression.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.Query<Person>(e => e.Id == 10045).FirstOrDefault();
}
```

Or like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.Query<Person>(e => e.FirstName == "John" && e.LastName == "Doe").FirstOrDefault();
}
```

> It always returns an `IEnumerable<T>` object even if your result is one.

#### Targetting a Table

You can also target a specific table by passing the literal table like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.Query("[dbo].[Person]",
		10045).FirstOrDefualt();
}
```

> The result is a list of dynamic objects of type `ExpandoObject`.

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.Query<Person>(10045,
		hints: "WITH (NOLOCK)").FirstOrDefault();
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.Query<Person>(10045,
		hints: SqlServerTableHints.TabLock).FirstOrDefault();
}
```

#### Ordering the Results

To order the results, you have to pass an array of `OrderField` objects in the `orderBy` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var orderBy = OrderField.Parse(new
	{
		LastName = Order.Descending,
		FirstName = Order.Ascending
	});
	var people = connection.Query<Person>(e => e.IsActive == true,
		orderBy: orderBy);
	// Do the stuffs for 'people' here
}
```

#### Filtering the Results

To filter the results, you have to pass a value at the `top` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.Query<Person>(e => e.IsActive == true,
		top: 100);
	// Do the stuffs for 'people' here
}
```

#### Caching the Results

To cache the results, simply pass a literal string key into the `cacheKey` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.Query<Person>(e => e.IsActive == true,
		cacheKey: "CackeKey:ActivePeople");
	// Do the stuffs for 'people' here
}
```

> The cache expiration is defaulted to `180` minutes. You can override it by passing an integer value at the `cacheExpirationInMinutes` argument.
