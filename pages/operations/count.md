---
layout: default
sidebar: operations
title: "Count"
permalink: /operation/count
tags: [repodb, tutorial, count]
parent: OPERATIONS
---

# Count

---

This method counts the number of rows in a table.

## Code Snippets

The following example counts rows in the `[dbo].[Person]` table inserted since yesterday.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1));
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.Count("[dbo].[Person]", new { State = "Michigan" });
}
```

Use [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) to build more complex WHERE expressions.

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

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
        hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.Count<Person>(e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
        hints: SqlServerTableHints.NoLock);
}
```
