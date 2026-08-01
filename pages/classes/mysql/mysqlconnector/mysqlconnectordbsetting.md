---
layout: default
sidebar: classes
title: "MySqlConnectorDbSetting"
description: "A setting class used for the MySqlConnector data provider."
permalink: /class/mysql/mysqlconnector/mysqlconnectordbsetting
tags: [repodb, mysqlconnectordbsetting, mysql, mysqlconnector]
parent: "MySQL"
grand_parent: CLASSES
nav_order: 10
---

# MySqlConnectorDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for [MySqlConnector](https://www.nuget.org/packages/MySqlConnector). It is automatically registered by [MySqlConnectorBootstrap](/class/mysql/mysqlconnector/mysqlconnectorbootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| AverageableType | `double` |
| ClosingQuote | `` ` `` |
| DefaultSchema | `null` |
| IsDirectionSupported | `false` |
| IsExecuteReaderDisposable | `true` |
| IsMultiStatementExecutable | `true` |
| IsPreparable | `true` |
| IsUseUpsert | `false` |
| OpeningQuote | `` ` `` |
| ParameterPrefix | `@` |

{: .note }
> `IsExecuteReaderDisposable` and `IsPreparable` are `true` here, whereas [MySqlDbSetting](/class/mysql/mysqldata/mysqldbsetting) (MySql.Data) sets both to `false`.

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(MySqlConnection), new MyCustomMySqlConnectorDbSetting(), true);
```
