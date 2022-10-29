---
layout: default
sidebar: operations
title: "SumAll"
permalink: /operation/sumall
tags: [repodb, tutorial, sumall]
parent: OPERATIONS
---

# SumAll

---

This method is used to compute the sum value of the target field.

## Code Snippets

Below is the sample code that returns the sum value of the column `Value` from a `[dbo].[Sales]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.SumAll<Sales>(e => e.Value);
}
```

## Targeting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.SumAll("[dbo].[Sales]",
        Field.From("Value"));
}
```

## Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.SumAll<Sales>(e => e.Value,
        hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.SumAll<Sales>(e => e.Value,
        hints: SqlServerTableHints.NoLock);
}
```
