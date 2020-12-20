---
layout: navpage
sidebar: operations
title: "Delete"
permalink: /operation/delete
tags: [repodb, tutorial, delete, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Delete

This method is used to delete the rows from the table.

#### Code Snippets

Below is the sample code to delete a row from the `[dbo].[Person]` table via data model.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete<Person>(person);
}
```

You can also delete directly by primary key.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete<Person>(10045);
}
```

#### Targeting a Table

You can also target a specific table by passing the literal table name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete("[dbo].[Person]", person);
}
```

Or, via primary key.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete("[dbo].[Person]", 10045);
}
```

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
