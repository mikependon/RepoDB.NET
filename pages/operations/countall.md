---
layout: default
sidebar: operations
title: "CountAll"
permalink: /operation/countall
tags: [repodb, tutorial, count]
parent: OPERATIONS
---

# CountAll

---

This method counts all rows in a table.

## Code Snippets

The following example counts all rows in the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.CountAll<Person>();
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.CountAll("[dbo].[Person]");
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.CountAll<Person>(hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.CountAll<Person>(hints: SqlServerTableHints.NoLock);
}
```
