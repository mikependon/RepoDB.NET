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

An extended query field class that is being used to define a query expression for the SQL statement using the LOWER function. It inherits the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new LowerQueryField("Column", "value");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the lowercase value of the `Column` is equals to `value`.

### GetString

The `GetString()` method returns a command text that utilizes the `LOWER` function.

```csharp
var where = new LowerQueryField("Column", "value");
var text = where.GetString(connection.GetDbSetting()); // Returns (LOWER([Column]) = @Column)
```
