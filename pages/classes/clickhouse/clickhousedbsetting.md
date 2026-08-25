---
layout: default
sidebar: classes
title: "ClickHouseDbSetting"
description: "A setting class used for the ClickHouse data provider."
permalink: /class/clickhouse/clickhousedbsetting
tags: [repodb, clickhousedbsetting, clickhouse]
parent: "ClickHouse"
grand_parent: CLASSES
---

# ClickHouseDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for ClickHouse, and implements [IClickHouseDbSetting](/interface/clickhouse/iclickhousedbsetting). It is automatically registered by [ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| ClosingQuote | `` ` `` |
| DefaultSchema | `null` |
| IsDirectionSupported | `false` |
| IsExecuteReaderDisposable | `false` |
| IsWaitForMutationsEnabled | `true` |
| IsMultiStatementExecutable | `false` |
| IsPreparable | `false` |
| IsUseUpsert | `false` |
| MultiStatementSeparator | `;` |
| OpeningQuote | `` ` `` |
| ParameterPrefix | `@` |

{: .note }
> `IsMultiStatementExecutable` is `false` — every SQL text method emits exactly one statement, so operations that need several steps (e.g. the bulk merge pipeline) issue them as separate round trips rather than one semicolon-joined batch.

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(ClickHouseConnection), new MyCustomClickHouseDbSetting(), true);
```
