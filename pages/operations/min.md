---
layout: default
sidebar: operations
title: "Min"
permalink: /operation/min
tags: [repodb, tutorial, min]
parent: OPERATIONS
---

# Min

---

This method computes the minimum value of the target field.

## Code Snippets

The following example returns the minimum value of the `Value` column from the `[dbo].[Sales]` table for a specific customer since yesterday.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customerExpenses = connection.Min<Sales>(e => e.Value,
        e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1));
}
```

## Targeting a Table

To target a specific table, pass the literal table and field name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customerExpenses = connection.Min("[dbo].[Sales]",
        Field.From("Value"), new { State = "Michigan" });
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
    var customerExpenses = connection.Min("[dbo].[Sales]", Field.From("Value"), where: where);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customerExpenses = connection.Min<Sales>(e => e.Value,
        e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
        hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customerExpenses = connection.Min<Sales>(
        e => e.CustomerId == 10045 && e.DateInsertedUtc >= DateTime.UtcNow.Date.AddDays(-1),
        hints: SqlServerTableHints.NoLock);
}
```
