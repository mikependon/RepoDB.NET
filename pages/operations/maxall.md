---
layout: default
sidebar: operations
title: "MaxAll"
permalink: /operation/maxall
tags: [repodb, tutorial, maxall]
parent: OPERATIONS
---

# MaxAll

---

This method computes the maximum value of the target field across all rows.

## Code Snippets

The following example returns the maximum value of the `Value` column from the `[dbo].[Sales]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.MaxAll<Sales>(e => e.Value);
}
```

## Targeting a Table

To target a specific table, pass the literal table and field name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.MaxAll("[dbo].[Sales]", Field.From("Value"));
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.MaxAll<Sales>(e => e.Value,
        hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.MaxAll<Sales>(e => e.Value,
        hints: SqlServerTableHints.NoLock);
}
```
