---
layout: default
sidebar: classes
title: "VerticaDbSetting"
description: "A setting class used for the Vertica data provider."
permalink: /class/vertica/verticadbsetting
tags: [repodb, verticadbsetting, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for Vertica. It is automatically registered by [VerticaBootstrap](/class/vertica/verticabootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| ClosingQuote | `"` |
| DefaultSchema | `null` |
| IsAffectedRowsSupported | `true` |
| IsDirectionSupported | `false` |
| IsExecuteReaderDisposable | `false` |
| IsInsertAllBatchable | `true` |
| IsMultiStatementExecutable | `false` |
| IsPreparable | `true` |
| IsTransactionSupported | `true` |
| IsUseUpsert | `true` |
| MaxParameterCount | `1500` |
| MultiStatementSeparator | `;` |
| OpeningQuote | `"` |
| ParameterPrefix | `@` |
| RequiresDbTypeBeforeValue | `true` |
| SkipsUnreferencedParameters | `true` |
| SqlTextParameterPrefix | `@` |

{: .note }
> `IsMultiStatementExecutable` is `false` — `VerticaCommand` refuses a compound `;`-separated statement once it carries a parameter — yet `IsInsertAllBatchable` is `true`, so [InsertAll](/operation/insertall) still batches multiple rows into one genuine multi-row `INSERT ... VALUES (...), (...), ...` statement. [MergeAll](/operation/mergeall)/[UpdateAll](/operation/updateall) have no equivalent single-statement shape and issue one round trip per row; passing an explicit `batchSize` greater than `1` to either throws a `NotSupportedException`.

{: .note }
> `RequiresDbTypeBeforeValue` is `true` — `Vertica.Data` lazily initializes internal parameter state inside `VerticaParameter.Type`'s setter and throws if `Value` is assigned first on a parameter fresh off `VerticaCommand.CreateParameter()`.

{: .note }
> `SkipsUnreferencedParameters` is `true` — Vertica strictly validates that every parameter bound to a command is actually referenced by a placeholder in the command text, rejecting the whole command otherwise (e.g. a null-valued equality filter rendered as a literal `IS NULL` with no placeholder at all).

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(VerticaConnection), new MyCustomVerticaDbSetting(), true);
```
