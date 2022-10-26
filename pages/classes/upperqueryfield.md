---
layout: default
sidebar: classes
title: "UpperQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the UPPER function."
permalink: /class/upperqueryfield
tags: [repodb, upperqueryfield]
parent: CLASSES
---

# UpperQueryField

---

An extended query field class that is being used to define a query expression for the SQL statement using the UPPER function. It inherits the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new UpperQueryField("Column", "VALUE");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the uppercase value of the `Column` is equals to `VALUE`.

### GetString

The `GetString()` method returns a command text that utilizes the `UPPER` function.

```csharp
var where = new UpperQueryField("Column", "VALUE");
var text = where.GetString(connection.GetDbSetting()); // Returns (UPPER([Column]) = @Column)
```
