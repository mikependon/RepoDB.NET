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

An extended query field class for defining SQL expressions using the `LEN` function. Inherits [QueryField](/class/queryfield).

{: .important }
> This functional query field is only supported for the SQL Server data provider.

## Usability

```csharp
var where = new LenQueryField("Column", 3);
var result = connection.Query<Entity>(where);
```

The result contains all records where the length of `Column` equals `3`.

## GetString

The `GetString()` method returns the command text using the `LEN` function.

```csharp
var where = new LenQueryField("Column", 3);
var text = where.GetString(connection.GetDbSetting()); // Returns (LEN([Column]) = @Column)
```
