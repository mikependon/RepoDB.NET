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

An extended query field class that is being used to define a query expression for the SQL statement using the RIGHT function. It inherits the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new RightQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the `Column` is ending with `Value`.

### GetString

The `GetString()` method returns a command text that utilizes the `RIGHT` function.

```csharp
var where = new RightQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (RIGHT([Column]) = @Column)
```
