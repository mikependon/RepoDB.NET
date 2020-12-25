---
layout: default
sidebar: operations
title: "CountAll"
permalink: /operation/countall
tags: [repodb, tutorial, count, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Operations
---

# CountAll

---

This method is used to count the number of rows from the table.

### Code Snippets

Below is the sample code that count all the rows from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.CountAll<Person>();
}
```

### Targeting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.CountAll("[dbo].[Person]");
}
```

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.CountAll<Person>(hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var counted = connection.CountAll<Person>(hints: SqlServerTableHints.NoLock);
}
```
