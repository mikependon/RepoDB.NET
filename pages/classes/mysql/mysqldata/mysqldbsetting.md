---
layout: default
sidebar: classes
title: "MySqlDbSetting"
description: "A setting class used for the MySql.Data data provider."
permalink: /class/mysql/mysqldata/mysqldbsetting
tags: [repodb, mysqldbsetting, mysql]
parent: "MySQL"
grand_parent: CLASSES
nav_order: 3
---

# MySqlDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for [MySql.Data](https://www.nuget.org/packages/MySql.Data). It is automatically registered by [MySqlBootstrap](/class/mysql/mysqldata/mysqlbootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| AverageableType | `double` |
| ClosingQuote | `` ` `` |
| DefaultSchema | `null` |
| IsDirectionSupported | `false` |
| IsExecuteReaderDisposable | `false` |
| IsMultiStatementExecutable | `true` |
| IsPreparable | `false` |
| IsUseUpsert | `false` |
| OpeningQuote | `` ` `` |
| ParameterPrefix | `@` |

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(MySqlConnection), new MyCustomMySqlDbSetting(), true);
```
