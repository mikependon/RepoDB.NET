---
layout: navpage
sidebar: mappers
title: "DbHelperMapper (RepoDb)"
permalink: /mapper/dbhelpermapper
tags: [repodb, class, dbhelpermapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# DbHelperMapper

A mapper class for the [IDbHelper](/interface/idbhelper)-based class. The mapping can be made based on the type of the target RDBMS data provider. Please visit the [Database Helper](/extensibility/databasehelper) for more information.

#### Methods

Below are the methods available from this class.

- `Add` - adds a mapping between the [IDbHelper](/interface/idbhelper) and the type of the `DbConnection`.
- `Get` - gets the mapped [IDbHelper](/interface/idbhelper) based on the type of the `DbConnection`.
- `Remove` - removed the mapping between the [IDbHelper](/interface/idbhelper) and the type of the `DbConnection`.

#### Use-Cases

You should use this class if you would like to override the default mapping of the library when it comes to database helper.

#### How to Use?

To add a mapping, simply call the `Add` method.

```csharp
DbHelperMapper.Add(typeof(SqlConnection), new OptimizedSqlServerDbHelper(), true);
```

> An exception will be be thrown if the mapping is already exists and you passed a `FALSE` value in the `override` argument.

To get the mapping, use the `Get` method.

```csharp
var helper = DbHelperMapper.Get(typeof(SqlConnection));
```

To remove the mapping, use the `Remove` method.

```csharp
var isRemoved = DbHelperMapper.Remove(typeof(SqlConnection), false);
```

> An exception will be be thrown if the mapping is not existing and you passed a `TRUE` value in the `throwException` argument.

