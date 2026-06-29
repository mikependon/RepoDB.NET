---
layout: default
sidebar: operations
title: "Exists"
permalink: /operation/exists
tags: [repodb, tutorial, exists]
parent: OPERATIONS
---

# Exists

---

This method checks whether matching rows exist in a table.

## Code Snippets

The following example checks whether a row exists in the `[dbo].[Person]` table by primary key.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var existing = connection.Exists<Person>(10045);
}
```

A LINQ expression can also be used:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var existing = connection.Exists<Person>(e =>
        e.Name == "Doe" && e.DateOfBirth =  DateTime.Parse("2000-01-01"));
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var existing = connection.Exists("[dbo].[Person]", 10045);
}
```

Use [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) to build more complex expressions.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField("Name", "Doe"),
        new QueryField("DateOfBirth", DateTime.Parse("2000-01-01"))
    };
    var existing = connection.Exists("[dbo].[Person]", where);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var existing = connection.Exists<Person>(10045,
    hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var existing = connection.Exists<Person>(10045,
    hints: SqlServerTableHints.NoLock);
}
```
