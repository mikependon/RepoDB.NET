---
layout: default
sidebar: classes
title: "PostgreSqlDbSetting"
description: "A setting class used for the PostgreSql data provider."
permalink: /class/postgresql/postgresqldbsetting
tags: [repodb, postgresqldbsetting, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for PostgreSQL. It is automatically registered by [PostgreSqlBootstrap](/class/postgresql/postgresqlbootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| AverageableType | `double` |
| ClosingQuote | `"` |
| DefaultSchema | `public` |
| IsDirectionSupported | `true` |
| IsExecuteReaderDisposable | `true` |
| IsMultiStatementExecutable | `true` |
| IsPreparable | `true` |
| IsUseUpsert | `false` |
| OpeningQuote | `"` |
| ParameterPrefix | `@` |

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(NpgsqlConnection), new MyCustomPostgreSqlDbSetting(), true);
```
