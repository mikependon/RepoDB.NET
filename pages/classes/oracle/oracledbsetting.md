---
layout: default
sidebar: classes
title: "OracleDbSetting"
description: "A setting class used for the Oracle data provider."
permalink: /class/oracle/oracledbsetting
tags: [repodb, oracledbsetting, oracle]
parent: "Oracle"
grand_parent: CLASSES
---

# OracleDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for Oracle. It is automatically registered by [OracleBootstrap](/class/oracle/oraclebootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| AverageableType | `double` |
| ClosingQuote | `"` |
| DefaultSchema | `null` |
| IsDirectionSupported | `true` |
| IsExecuteReaderDisposable | `true` |
| IsMultiStatementExecutable | `false` |
| IsPreparable | `true` |
| IsUseUpsert | `false` |
| OpeningQuote | `"` |
| ParameterPrefix | `:` |

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(OracleConnection), new MyCustomOracleDbSetting(), true);
```
