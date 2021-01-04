---
layout: default
sidebar: operations
title: "Truncate"
permalink: /operation/truncate
tags: [repodb, tutorial, count, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: OPERATIONS
---

# Truncate

---

This method is used to truncates a table from the database.

### Code Snippets

Below is the sample code that truncates the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Truncate<Person>();
}
```

### Targeting a Table

You can also target a specific table by passing the literal table and field name like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Truncate("[dbo].[Person]");
}
```
