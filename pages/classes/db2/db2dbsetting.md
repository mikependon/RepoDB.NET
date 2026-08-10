---
layout: default
sidebar: classes
title: "Db2DbSetting"
description: "A setting class used for the Db2 data provider."
permalink: /class/db2/db2dbsetting
tags: [repodb, db2dbsetting, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2DbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for Db2. It is automatically registered by [Db2Bootstrap](/class/db2/db2bootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| AverageableType | `double` |
| ClosingQuote | `"` |
| DefaultSchema | `null` |
| IsDirectionSupported | `true` |
| IsExecuteReaderDisposable | `true` |
| IsMultiStatementExecutable | `true` |
| IsPreparable | `true` |
| IsUseUpsert | `false` |
| MultiStatementSeparator | `;` |
| OpeningQuote | `"` |
| ParameterPrefix | `:` |

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(DB2Connection), new MyCustomDb2DbSetting(), true);
```
