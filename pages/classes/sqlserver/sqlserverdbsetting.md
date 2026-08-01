---
layout: default
sidebar: classes
title: "SqlServerDbSetting"
description: "A setting class used for the SQL Server data provider."
permalink: /class/sqlserver/sqlserverdbsetting
tags: [repodb, sqlserverdbsetting, sqlserver]
parent: "SQL Server"
grand_parent: CLASSES
---

# SqlServerDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for SQL Server. It is automatically registered by [SqlServerBootstrap](/class/sqlserver/sqlserverbootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `true` |
| AverageableType | `double` |
| ClosingQuote | `]` |
| DefaultSchema | `dbo` |
| IsDirectionSupported | `true` |
| IsExecuteReaderDisposable | `true` |
| IsMultiStatementExecutable | `true` |
| IsPreparable | `true` |
| IsUseUpsert | `false` |
| OpeningQuote | `[` |
| ParameterPrefix | `@` |

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(SqlConnection), new MyCustomSqlServerDbSetting(), true);
```
