---
layout: page
title: "DbSettingMapper (RepoDb)"
permalink: /mapper/dbsettingmapper
tags: [repodb, class, dbsettingmapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## DbSettingMapper

A mapper class for the [IDbSetting](/interface/idbsetting)-based class. The mapping can be made based on the type of the target RDBMS data provider. Please visit the [Database Setting](/extensibility/databasesetting) for more information.

#### Methods

Below are the methods available from this class.

- Add - adds a mapping between the [IDbSetting](/interface/idbsetting) and the type of the `DbConnection`.
- Get - gets the mapped [IDbSetting](/interface/idbsetting) based on the type of the `DbConnection`.
- Remove - removed the mapping between the [IDbSetting](/interface/idbsetting) and the type of the `DbConnection`.

#### Use-Cases

You should use this class if you would like to override the default mapping of the library when it comes to database setting.

#### How to Use?

To add a mapping, simply call the `Add` method.

```csharp
DbSettingMapper.Add(typeof(SqlConnection), new OptimizedSqlServerDbSetting(), true);
```

> An exception will be be thrown if the mapping is already exists and you passed a `FALSE` value in the `override` argument.

To get the mapping, use the `Get` method.

```csharp
var helper = DbSettingMapper.Get(typeof(SqlConnection));
```

To remove the mapping, use the `Remove` method.

```csharp
var isRemoved = DbSettingMapper.Remove(typeof(SqlConnection), false);
```

> An exception will be be thrown if the mapping is not existing and you passed a `TRUE` value in the `throwException` argument.

