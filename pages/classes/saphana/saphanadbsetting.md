---
layout: default
sidebar: classes
title: "SapHanaDbSetting"
description: "A setting class used for the SAP HANA data provider."
permalink: /class/saphana/saphanadbsetting
tags: [repodb, saphanadbsetting, saphana]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for SAP HANA. It is automatically registered by [SapHanaBootstrap](/class/saphana/saphanabootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| ClosingQuote | `"` |
| DefaultSchema | `null` |
| IsAffectedRowsSupported | `true` |
| IsDirectionSupported | `false` |
| IsExecuteReaderDisposable | `true` |
| IsMultiStatementExecutable | `false` |
| IsPreparable | `true` |
| IsTransactionSupported | `true` |
| IsUseUpsert | `false` |
| MaxParameterCount | `2098` |
| MultiStatementSeparator | `;` |
| OpeningQuote | `"` |
| ParameterPrefix | `:` |
| RequiresDbTypeBeforeValue | `false` |
| SkipsUnreferencedParameters | `false` |
| SqlTextParameterPrefix | `:` |

{: .note }
> `IsMultiStatementExecutable` is `false` — SAP HANA's ADO.NET client rejects a command text containing more than one SQL statement. Unlike [VerticaDbSetting](/class/vertica/verticadbsetting), there is no `IsInsertAllBatchable` override here, so [InsertAll](/operation/insertall)/[MergeAll](/operation/mergeall)/[UpdateAll](/operation/updateall) all issue one round trip per row; passing an explicit `batchSize` greater than `1` to any of them throws a `NotSupportedException`.

{: .note }
> Unlike Firebird, Oracle, and Vertica, no `TrimTrailingSemicolon` step is needed anywhere in [SapHanaStatementBuilder](/class/saphana/saphanastatementbuilder) — SAP HANA's ADO.NET client accepts a single trailing `;` on an otherwise-standalone statement; it only rejects *multiple* statements in one command text.

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(HanaConnection), new MyCustomSapHanaDbSetting(), true);
```
