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

This method is used to delete an array-of (and/or all) rows from the table.

### Code Snippets

Below is the sample code that delete all the rows from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.DeleteAll<Person>();
}
```

Or you can target the list of primary keys.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var primaryKeys = new [] { 10045, 11921, 12001 }; 
    var deletedRows = connection.DeleteAll<Person>(primaryKeys);
}
```

### Targeting a Table

You can also target a specific table by passing the literal table name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.DeleteAll("[dbo].[Person]");
}
```

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.DeleteAll<Person>(hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.DeleteAll<Person>(hints: SqlServerTableHints.TabLock);
}
```
