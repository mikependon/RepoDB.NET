---
layout: default
sidebar: operations
title: "Average"
permalink: /operation/average
tags: [repodb, tutorial, average]
parent: OPERATIONS
---

# Average

---

This method computes the average value of the target field.

## Code Snippets

The following example averages the `Value` column from the `[dbo].[Sales]` table for a specific customer since yesterday.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customerExpenses = connection.Average<Sales>(e => e.Value,
        e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1));
}
```

## Targeting a Table

To target a specific table, pass the literal table and field name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customerExpenses = connection.Average("[dbo].[Sales]", Field.From("Value"), new { State = "Michigan" });
}
```

Use [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) to build more complex WHERE expressions.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField("CustomerId", 10045),
        new QueryField("DateInsertedUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1))
    }
    var customerExpenses = connection.Average("[dbo].[Sales]", Field.From("Value"), where);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customerExpenses = connection.Average<Sales>(e => e.Value,
        e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
        hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customerExpenses = connection.Average<Sales>(e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
        hints: SqlServerTableHints.NoLock);
}
```
