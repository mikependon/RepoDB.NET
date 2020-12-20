---
layout: navpage
sidebar: classes
title: "SqlServerTableHints"
description: "A class that contains all the SQL Server table optimizers."
permalink: /class/sqlservertablehints
tags: [repodb, class, sqlservertablehints, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# SqlServerTableHints

This class contains the list of table hints that is useful for SQL Server. It is usually used as passing parameter to the `hints` argument of most operations.

#### How to use?

To use the hints, simply pass the target hints you want to use.

Below is a sample for querying the dirty records.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.QueryAll<Person>(hints: SqlServerTableHints.NoLock);
}
```

Or only reading the commited records.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.QueryAll<Person>(hints: SqlServerTableHints.ReadPast);
}
```

Also, below is a sample to lock the table during insertion.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person>(person, hints: SqlServerTableHints.TabLock);
}
```

Or during the [Update](/operation/update) operation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Update<Person>(person, hints: SqlServerTableHints.TabLock);
}
```

> The class contains a lot of hints, we recommend that you visits the official Microsoft [documentation](https://docs.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-table?view=sql-server-2017) for further understanding.
