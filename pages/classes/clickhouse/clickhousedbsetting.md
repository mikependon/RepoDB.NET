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
| IsWaitForMutationsEnabled | `false` |
| IsMultiStatementExecutable | `false` |
| IsPreparable | `false` |
| IsUseUpsert | `false` |
| MultiStatementSeparator | `;` |
| OpeningQuote | `` ` `` |
| ParameterPrefix | `@` |

{: .note }
> `IsMultiStatementExecutable` is `false` — every SQL text method emits exactly one statement, so operations that need several steps (e.g. the bulk merge pipeline) issue them as separate round trips rather than one semicolon-joined batch.

{: .note }
> The bare `new ClickHouseDbSetting()` constructor defaults `IsWaitForMutationsEnabled` to `true`, but [ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap) (via `ClickHouseGlobalConfiguration.UseClickHouse()`) registers it with `false` unless the caller opts in with `UseClickHouse(isWaitForMutationsEnabled: true)`. This setting only affects `BulkMerge`/`BulkUpdate`/`BulkDelete` from [RepoDb.ClickHouse.BulkOperations](/release/clickhousebulk) — the plain [Update](/operation/update)/[UpdateAll](/operation/updateall)/[Delete](/operation/delete)/[DeleteAll](/operation/deleteall) operations never consult it and always return as soon as their mutation is queued. See the [Get Started for ClickHouse](/tutorial/get-started-clickhouse) page for details.

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(ClickHouseConnection), new MyCustomClickHouseDbSetting(), true);
```
