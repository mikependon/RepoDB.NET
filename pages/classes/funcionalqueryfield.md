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

An extended query field class for defining functional SQL expressions. Useful for applying SQL functions to query conditions. Inherits [QueryField](/class/queryfield).

## Usability

The following example applies the `TRIM` function to a column condition.

```csharp
var where = new FunctionalQueryField("Column", "Value", "TRIM({0})");
var result = connection.Query<Entity>(where);
```

The result contains all records where the trimmed value of `Column` equals `Value`.

## GetString

The `GetString()` method returns the SQL command text with the specified function applied.

```csharp
var where = new FunctionalQueryField("Column", "Value", "TRIM({0})");
var text = where.GetString(connection.GetDbSetting()); // Returns (TRIM([Column]) = @Column)
```

## Formatting

The library uses `string.Format()` to compose the expression. In the `format` argument, use `{0}` as the placeholder for the field name.

```csharp
var where = new FunctionalQueryField("Column", "Value", "LEN({0}) > 5 AND RIGHT({0})");
var text = where.GetString(connection.GetDbSetting()); // Returns (LEN([Column]) > 5 AND RIGHT([Column]) = @Column)
```
