---
layout: default
sidebar: classes
title: "TrimQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the TRIM function."
permalink: /class/trimqueryfield
tags: [repodb, trimqueryfield]
parent: CLASSES
---

# TrimQueryField

---

An extended query field class that is being used to define a query expression for the SQL statement using the TRIM function. It inherits the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new TrimQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the trimmed value of `Column` is equals to `Value`.

### GetString

The `GetString()` method returns a command text that utilizes the `TRIM` function.

```csharp
var where = new TrimQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (TRIM([Column]) = @Column)
```
