---
layout: default
sidebar: operations
title: "Delete"
permalink: /operation/delete
tags: [repodb, tutorial, delete]
parent: OPERATIONS
---

# Delete

---

This method deletes rows from a table.

## Code Snippets

The following example deletes a row from the `[dbo].[Person]` table using a data model.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete<Person>(person);
}
```

To delete by primary key:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete<Person>(10045);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete("[dbo].[Person]", person);
}
```

Or by primary key:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete("[dbo].[Person]", 10045);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
