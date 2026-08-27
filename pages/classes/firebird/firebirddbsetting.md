---
layout: default
sidebar: classes
title: "FirebirdDbSetting"
description: "A setting class used for the Firebird data provider."
permalink: /class/firebird/firebirddbsetting
tags: [repodb, firebirddbsetting, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for Firebird. It is automatically registered by [FirebirdBootstrap](/class/firebird/firebirdbootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| ClosingQuote | `"` |
| DefaultSchema | `null` |
| IsAffectedRowsSupported | `true` |
| IsDirectionSupported | `false` |
| IsExecuteReaderDisposable | `false` |
| IsMultiStatementExecutable | `false` |
| IsPreparable | `true` |
| IsTransactionSupported | `true` |
| IsUseUpsert | `false` |
| MaxParameterCount | `1500` |
| MultiStatementSeparator | `;` |
| OpeningQuote | `"` |
| ParameterPrefix | `@` |
| SqlTextParameterPrefix | `@` |

{: .note }
> `IsMultiStatementExecutable` is `false` — `FbCommand` cannot execute multiple statements in a single round trip, so [InsertAll](/operation/insertall), [MergeAll](/operation/mergeall) and [UpdateAll](/operation/updateall) issue one statement per row instead of a single batched command. Passing an explicit `batchSize` greater than `1` to any of them throws a `NotSupportedException`.

{: .note }
> `MaxParameterCount` is `1500` — lower than the `2098` most other providers default to — reflecting Firebird's DSQL parser rejecting an `IN (...)` list past roughly 1500 members with `"Implementation limit exceeded"`.

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(FbConnection), new MyCustomFirebirdDbSetting(), true);
```
