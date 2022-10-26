---
layout: default
sidebar: classes
title: "LengthQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the LENGTH function."
permalink: /class/lengthqueryfield
tags: [repodb, lengthqueryfield]
parent: CLASSES
---

# LengthQueryField

---

An extended query field class that is being used to define a query expression for the SQL statement using the LENGTH function. It inherits the [QueryField](/class/queryfield) object.

> This functional query field object is only useful for PostgreSQL, MySQL and SQLite data providers.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new LengthQueryField("Column", 3);
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the lenght of the `Column` is equals `3`.

### GetString

The `GetString()` method returns a command text that utilizes the `LENGTH` function.

```csharp
var where = new LengthQueryField("Column", 3);
var text = where.GetString(connection.GetDbSetting()); // Returns (LENGTH([Column]) = @Column)
```
