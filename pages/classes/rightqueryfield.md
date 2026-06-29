---
layout: default
sidebar: classes
title: "RightQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the RIGHT function."
permalink: /class/rightqueryfield
tags: [repodb, rightqueryfield]
parent: CLASSES
---

# RightQueryField

---

An extended query field class for defining SQL expressions using the `RIGHT` function. Inherits [QueryField](/class/queryfield).

## Usability

```csharp
var where = new RightQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result contains all records where `Column` ends with `Value`.

## GetString

The `GetString()` method returns the command text using the `RIGHT` function.

```csharp
var where = new RightQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (RIGHT([Column]) = @Column)
```
