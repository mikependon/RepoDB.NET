---
layout: default
sidebar: classes
title: "LengthQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the LENGTH function."
permalink: /class/lengthqueryfield
tags: [repodb, lengthqueryfield]
parent: CLASSES
---

# LengthQueryField

---

An extended query field class for defining SQL expressions using the `LENGTH` function. Inherits [QueryField](/class/queryfield).

{: .important }
> This functional query field is only supported for PostgreSQL, MySQL, and SQLite data providers.

## Usability

```csharp
var where = new LengthQueryField("Column", 3);
var result = connection.Query<Entity>(where);
```

The result contains all records where the length of `Column` equals `3`.

## GetString

The `GetString()` method returns the command text using the `LENGTH` function.

```csharp
var where = new LengthQueryField("Column", 3);
var text = where.GetString(connection.GetDbSetting()); // Returns (LENGTH([Column]) = @Column)
```
