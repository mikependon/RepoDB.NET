---
layout: default
sidebar: operations
title: "Truncate"
permalink: /operation/truncate
tags: [repodb, tutorial, count]
parent: OPERATIONS
---

# Truncate

---

This method truncates a table in the database.

## Code Snippets

The following example truncates the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Truncate<Person>();
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Truncate("[dbo].[Person]");
}
```
