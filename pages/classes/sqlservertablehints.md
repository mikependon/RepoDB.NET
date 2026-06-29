---
layout: default
sidebar: classes
title: "SqlServerTableHints"
description: "A class that contains all the SQL Server table optimizers."
permalink: /class/sqlservertablehints
tags: [repodb, sqlservertablehints]
parent: CLASSES
---

# SqlServerTableHints

---

This class contains SQL Server table hints for use with the `hints` argument of most operations.

## Usability

Pass the desired hint to the `hints` argument.

Query with dirty reads.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.QueryAll<Person>(hints: SqlServerTableHints.NoLock);
}
```

Query only committed records.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.QueryAll<Person>(hints: SqlServerTableHints.ReadPast);
}
```

Lock the table during insertion.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person>(person, hints: SqlServerTableHints.TabLock);
}
```

Or during an [Update](/operation/update) operation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Update<Person>(person, hints: SqlServerTableHints.TabLock);
}
```

{: .note }
> This class contains many additional hints. Refer to the official Microsoft [documentation](https://docs.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-table?view=sql-server-2017) for the full reference.
