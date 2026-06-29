---
layout: default
sidebar: operations
title: "AverageAll"
permalink: /operation/averageall
tags: [repodb, tutorial, averageall]
parent: OPERATIONS
---

# AverageAll

---

This method computes the average value of the target field across all rows.

## Code Snippets

The following example averages the `Value` column across all rows in the `[dbo].[Sales]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.AverageAll<Sales>(e => e.Value);
}
```

## Targeting a Table

To target a specific table, pass the literal table and field name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.AverageAll("[dbo].[Sales]",
        Field.From("Value"));
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.AverageAll<Sales>(e => e.Value,
        hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.AverageAll<Sales>(e => e.Value,
        hints: SqlServerTableHints.NoLock);
}
```
