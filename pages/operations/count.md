---
layout: navpage
sidebar: operations
title: "Count"
permalink: /operation/count
tags: [repodb, tutorial, count, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Count

This method is used to count the number of rows from the table.

#### Code Snippets

Below is the sample code that counts the rows from the `[dbo].[Person]` table since yesterday.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1));
}
```

#### Targeting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var counted = connection.Count("[dbo].[Person]", new { State = "Michigan" });
}
```

Or, use the [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) if you are to enhance the WHERE expressions.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var where = new []
	{
		new QueryField("DateInsertedUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1))
	}
	var counted = connection.Count("[dbo].[Person]", where: where);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
		hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
		hints: SqlServerTableHints.NoLock);
}
```
