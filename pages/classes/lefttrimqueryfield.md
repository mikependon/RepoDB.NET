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

An extended query field class that is being used to define a query expression for the SQL statement using the LTRIM function. It inherits the [QueryField](/class/queryfield) object.

## Usability

Below is a sample code on how to use this class.

```csharp
var where = new LeftTrimQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the left-trimmed value of `Column` is equals to `Value`.

## GetString

The `GetString()` method returns a command text that utilizes the `LTRIM` function.

```csharp
var where = new LeftTrimQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (LTRIM([Column]) = @Column)
```
