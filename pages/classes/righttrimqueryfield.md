---
layout: default
sidebar: classes
title: "RightTrimQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the RTRIM function."
permalink: /class/righttrimqueryfield
tags: [repodb, righttrimqueryfield]
parent: CLASSES
---

# RightTrimQueryField

---

An extended query field class for defining SQL expressions using the `RTRIM` function. Inherits [QueryField](/class/queryfield).

## Usability

```csharp
var where = new RightTrimQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result contains all records where the right-trimmed value of `Column` equals `Value`.

## GetString

The `GetString()` method returns the command text using the `RTRIM` function.

```csharp
var where = new RightTrimQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (RTRIM([Column]) = @Column)
```
