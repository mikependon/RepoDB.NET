---
layout: default
sidebar: classes
title: "EnterpriseDbDbSetting"
description: "A setting class used for the EnterpriseDb (EDBConnection) data provider."
permalink: /class/enterprisedb/enterprisedbdbsetting
tags: [repodb, enterprisedbdbsetting, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbDbSetting

---

This class is the [BaseDbSetting](/class/basedbsetting)-derived implementation for EnterpriseDB. It is automatically registered by [EnterpriseDbBootstrap](/class/enterprisedb/enterprisedbbootstrap) — you do not need to instantiate it directly under normal use.

## Configured Values

| Property | Value |
|:---------|:------|
| AreTableHintsSupported | `false` |
| ClosingQuote | `"` |
| DefaultSchema | `public` |
| IsAffectedRowsSupported | `true` |
| IsDirectionSupported | `true` |
| IsExecuteReaderDisposable | `true` |
| IsMultiStatementExecutable | `true` |
| IsPreparable | `true` |
| IsTransactionSupported | `true` |
| IsUseUpsert | `false` |
| OpeningQuote | `"` |
| ParameterPrefix | `@` |
| SqlTextParameterPrefix | `@` |

## Usability

Use [DbSettingMapper](/mapper/dbsettingmapper) to override it with a custom implementation.

```csharp
DbSettingMapper.Add(typeof(EDBConnection), new MyCustomEnterpriseDbDbSetting(), true);
```
