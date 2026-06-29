---
layout: default
sidebar: classes
title: "LowerQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the LOWER function."
permalink: /class/lowerqueryfield
tags: [repodb, lowerqueryfield]
parent: CLASSES
---

# LowerQueryField

---

An extended query field class for defining SQL expressions using the `LOWER` function. Inherits [QueryField](/class/queryfield).

## Usability

```csharp
var where = new LowerQueryField("Column", "value");
var result = connection.Query<Entity>(where);
```

The result contains all records where the lowercase value of `Column` equals `value`.

## GetString

The `GetString()` method returns the command text using the `LOWER` function.

```csharp
var where = new LowerQueryField("Column", "value");
var text = where.GetString(connection.GetDbSetting()); // Returns (LOWER([Column]) = @Column)
```
