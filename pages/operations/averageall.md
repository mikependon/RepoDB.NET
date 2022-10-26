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

This method is used to compute the average value of the target field.

### Code Snippets

Below is the sample code that averages all the rows' column `Value` from a `[dbo].[Sales]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.AverageAll<Sales>(e => e.Value);
}
```

### Targeting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.AverageAll("[dbo].[Sales]",
        Field.From("Value"));
}
```

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.AverageAll<Sales>(e => e.Value,
        hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var expenses = connection.AverageAll<Sales>(e => e.Value,
        hints: SqlServerTableHints.NoLock);
}
```
