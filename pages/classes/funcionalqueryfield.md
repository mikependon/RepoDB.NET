---
layout: default
sidebar: classes
title: "FunctionalQueryField"
description: "A query field class that is being used to define a functional query expression for the SQL statement that is useful for customizations."
permalink: /class/functionalqueryfield
tags: [repodb, functionalqueryfield]
parent: CLASSES
---

# FunctionalQueryField

---

An extended query field class that is being used to define a functional query expression for the SQL statement. This class is very useful if you wish to customize your query expression in many ways. It inherits the [QueryField](/class/queryfield) object.

### Usability

Below is a sample code that is using the `TRIM` function of the underlying database.

```csharp
var where = new FunctionalQueryField("Column", "Value", "TRIM({0})");
var result = connection.Query<Entity>(where);
```

The result would contain all the records where the trimmed value of the `Column` is equals to `Value`.

### GetString

The `GetString()` method returns a command text that utilizes the passed formatted function.

```csharp
var where = new FunctionalQueryField("Column", "Value", "TRIM({0})");
var text = where.GetString(connection.GetDbSetting()); // Returns (TRIM([Column]) = @Column)
```

### Formatting

It is important to take note that the library is using the `string.Format()` method to compose the formatting of the expression. Therefore, in the `format` argument of the constructor, always put the `{0}` format indexer as a placement of the actual field.

See the complex formatting below.

```csharp
var where = new FunctionalQueryField("Column", "Value", "LEN({0}) > 5 AND RIGHT({0})");
var text = where.GetString(connection.GetDbSetting()); // Returns (LEN([Column]) > 5 AND RIGHT([Column]) = @Column)
```
