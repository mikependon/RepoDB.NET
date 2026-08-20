---
layout: default
sidebar: classes
title: "MariaDbDbSetting"
description: "A setting class used for the MySql.Data-based MariaDb data provider."
permalink: /class/mariadb/mariadb/mariadbdbsetting
tags: [repodb, mariadbdbsetting, mariadb]
parent: "MariaDb"
grand_parent: "MariaDB"
nav_order: 3
---

# MariaDbDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for the `MySql.Data`-based [RepoDb.MariaDb](https://www.nuget.org/packages/RepoDb.MariaDb) package. It is automatically registered by [MariaDbBootstrap](/class/mariadb/mariadb/mariadbbootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| ClosingQuote | `` ` `` |
| DefaultSchema | `null` |
| IsDirectionSupported | `false` |
| IsExecuteReaderDisposable | `false` |
| IsMultiStatementExecutable | `true` |
| IsPreparable | `false` |
| IsUseUpsert | `false` |
| MultiStatementSeparator | `;` |
| OpeningQuote | `` ` `` |
| ParameterPrefix | `@` |

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(MariaDbConnection), new MyCustomMariaDbDbSetting(), true);
```
