---
layout: navpage
sidebar: operations
title: "Exists"
permalink: /operation/exists
tags: [repodb, tutorial, exists, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Exists

This method is used to check whether the rows are existing in the table.

#### Code Snippets

Below is a sample code to check whether a row is existing from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists<Person>(10045);
}
```

Or, you can use the `Linq` to enhance the expression.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists<Person>(e => e.Name == "Doe" && e.DateOfBirth =  DateTime.Parse("2000-01-01"));
}
```

#### Targeting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists("[dbo].[Person]", 10045);
}
```

Or, you can use the [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) to enhance the expression.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var where = new []
	{
		new QueryField("Name", "Doe"),
		new QueryField("DateOfBirth", DateTime.Parse("2000-01-01"))
	};
	var existing = connection.Exists("[dbo].[Person]", where);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists<Person>(10045,
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var existing = connection.Exists<Person>(10045,
		hints: SqlServerTableHints.NoLock);
}
```
