---
layout: default
sidebar: operations
title: "DeleteAll"
permalink: /operation/deleteall
tags: [repodb, tutorial, count]
parent: OPERATIONS
---

# DeleteAll

---

This method deletes all rows, or a specified set of rows by primary key, from a table.

## Code Snippets

The following example deletes all rows from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

To delete by a list of primary keys:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var primaryKeys = new [] { 10045, 11921, 12001 }; 
    var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.DeleteAll("[dbo].[Person]");
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.DeleteAll<Person>(hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.DeleteAll<Person>(hints: SqlServerTableHints.TabLock);
}
```
