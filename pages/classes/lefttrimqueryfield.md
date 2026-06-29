---
layout: default
sidebar: classes
title: "LeftTrimQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the LTRIM function."
permalink: /class/lefttrimqueryfield
tags: [repodb, lefttrimqueryfield]
parent: CLASSES
---

# LeftTrimQueryField

---

An extended query field class for defining SQL expressions using the `LTRIM` function. Inherits [QueryField](/class/queryfield).

## Usability

```csharp
var where = new LeftTrimQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result contains all records where the left-trimmed value of `Column` equals `Value`.

## GetString

The `GetString()` method returns the command text using the `LTRIM` function.

```csharp
var where = new LeftTrimQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (LTRIM([Column]) = @Column)
```
