---
layout: default
sidebar: classes
title: "SqLiteDbSetting"
description: "A setting class used for the SQLite data provider."
permalink: /class/sqlite/sqlitedbsetting
tags: [repodb, sqlitedbsetting, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# SqLiteDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for SQLite. It is automatically registered by [SqliteBootstrap](/class/sqlite/sqlitebootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| AverageableType | `double` |
| ClosingQuote | `]` |
| DefaultSchema | `null` |
| IsDirectionSupported | `false` |
| IsExecuteReaderDisposable | `true` (`false` when constructed by [SqliteBootstrap](/class/sqlite/sqlitebootstrap)) |
| IsMultiStatementExecutable | `true` |
| IsPreparable | `true` |
| IsUseUpsert | `true` |
| OpeningQuote | `[` |
| ParameterPrefix | `@` |

{: .note }
> The constructor accepts an `isExecuteReaderDisposable` flag (defaults to `true`). [SqliteBootstrap](/class/sqlite/sqlitebootstrap) explicitly passes `false`, since the `Microsoft.Data.Sqlite` reader it pairs with should not be treated as disposable in that flow.

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(SqliteConnection), new MyCustomSqLiteDbSetting(), true);
```
