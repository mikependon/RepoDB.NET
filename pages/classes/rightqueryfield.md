---
layout: default
sidebar: classes
title: "RightQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the RIGHT function."
permalink: /class/rightqueryfield
tags: [repodb, class, rightqueryfield, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: CLASSES
---

# RightQueryField

---

An extended query field class that is being used to define a query expression for the SQL statement using the RIGHT function. In inherits from the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new RightQueryField("Name", "sen");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the `Name` column is ending with `sen`.

### GetString

The `GetString()` method returns a command text that utilizes the `RIGHT` function.

```csharp
var where = new RightQueryField("Name", "sen");
var text = where.GetString(connection.GetDbSetting()); // Returns (RIGHT([Name]) = @Name)
```
