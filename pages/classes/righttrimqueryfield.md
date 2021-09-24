---
layout: default
sidebar: classes
title: "RightTrimQueryField"
description: "A query field class that is being used to define a query expression for the SQL statement using the RTRIM function."
permalink: /class/righttrimqueryfield
tags: [repodb, class, righttrimqueryfield, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: CLASSES
---

# RightTrimQueryField

---

An extended query field class that is being used to define a query expression for the SQL statement using the RTRIM function. It inherits the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code on how to use this class.

```csharp
var where = new RightTrimQueryField("Column", "Value");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the right-trimmed value of `Column` is equals to `Value`.

### GetString

The `GetString()` method returns a command text that utilizes the `RTRIM` function.

```csharp
var where = new RightTrimQueryField("Column", "Value");
var text = where.GetString(connection.GetDbSetting()); // Returns (RTRIM([Column]) = @Column)
```
