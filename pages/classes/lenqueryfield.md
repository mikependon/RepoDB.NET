---
layout: default
sidebar: classes
title: "LenQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the LEN function."
permalink: /class/lenqueryfield
tags: [repodb, lenqueryfield]
parent: CLASSES
---

# LenQueryField

---

An extended query field class that is being used to define a query expression for the SQL statement using the LEN function. It inherits the [QueryField](/class/queryfield) object.

> This functional query field object is only useful for SQL Server data provider.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new LenQueryField("Column", 3);
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the lenght of the `Column` is equals `3`.

### GetString

The `GetString()` method returns a command text that utilizes the `LEN` function.

```csharp
var where = new LenQueryField("Column", 3);
var text = where.GetString(connection.GetDbSetting()); // Returns (LEN([Column]) = @Column)
```
