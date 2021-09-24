---
layout: default
sidebar: classes
title: "LeftQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the LEFT function."
permalink: /class/leftqueryfield
tags: [repodb, class, leftqueryfield, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: CLASSES
---

# LeftQueryField

---

An extended query field class that is being used to define a query expression for the SQL statement using the LEFT function. In inherits from the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new LeftQueryField("Country", "Den");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the `Country` column is starting with `Den`.

### GetString

The `GetString()` method returns a command text that utilizes the `LEFT` function.

```csharp
var where = new LeftQueryField("Country", "Den");
var text = where.GetString(connection.GetDbSetting()); // Returns (LEFT([Country]) = @Country)
```
