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

An extended query field class that is being used to define a query expression for the SQL statement using the LEFT function. It inherits the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new LeftQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the `Column` is starting with `Value`.

### GetString

The `GetString()` method returns a command text that utilizes the `LEFT` function.

```csharp
var where = new LeftQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (LEFT([Column]) = @Column)
```
