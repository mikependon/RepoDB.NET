---
layout: default
sidebar: mappers
title: "DbHelperMapper"
description: "A mapper class for all the database helper classes. The mapping can be made based on the type of the target RDBMS data provider."
permalink: /mapper/dbhelpermapper
tags: [repodb, dbhelpermapper]
parent: MAPPERS
---

# DbHelperMapper

---

A mapper class for the [IDbHelper](/interface/idbhelper)-based class. The mapping can be made based on the type of the target RDBMS data provider. Please visit the [Database Helper](/extensibility/databasehelper) for more information.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Add | Adds a mapping between the [IDbHelper](/interface/idbhelper) and the type of the `DbConnection`. |
| Clear | Clears all the existing mappings of the database helpers. |
| Get | Gets the existing mapped [IDbHelper](/interface/idbhelper) object based on the type of the `DbConnection`. |
| Remove | Removes the existing mapped [IDbHelper](/interface/idbhelper) object based on the type of the `DbConnection`. |

## Usability

To add a mapping, simply call the `Add()` method.

```csharp
DbHelperMapper.Add<SqlConnection>(new OptimizedSqlServerDbHelper(), true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get()` method.

```csharp
var helper = DbHelperMapper.Get<SqlConnection>();
```

To remove the mapping, use the `Remove()` method.

```csharp
DbHelperMapper.Remove<SqlConnection>();
```

