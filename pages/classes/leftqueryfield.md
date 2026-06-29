---
layout: default
sidebar: classes
title: "LeftQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the LEFT function."
permalink: /class/leftqueryfield
tags: [repodb, leftqueryfield]
parent: CLASSES
---

# LeftQueryField

---

An extended query field class for defining SQL expressions using the `LEFT` function. Inherits [QueryField](/class/queryfield).

## Usability

```csharp
var where = new LeftQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result contains all records where `Column` starts with `Value`.

## GetString

The `GetString()` method returns the command text using the `LEFT` function.

```csharp
var where = new LeftQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (LEFT([Column]) = @Column)
```
