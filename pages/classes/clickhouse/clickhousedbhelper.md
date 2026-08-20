---
layout: default
sidebar: classes
title: "ClickHouseDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a ClickHouse table."
permalink: /class/clickhouse/clickhousedbhelper
tags: [repodb, clickhousedbhelper, clickhouse]
parent: "ClickHouse"
grand_parent: CLASSES
---

# ClickHouseDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for ClickHouse. It queries the `system.columns` system table, filtered by `database` and `table`, to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements. Nullability is derived from a column's `Nullable(...)` type wrapper, and `IsIdentity` is always reported `false` — ClickHouse has no identity/auto-increment concept.

It is automatically registered by [ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a ClickHouse column data type (e.g. `Nullable(DateTime64(3))`) into its equivalent .NET CLR type. Defaults to [ClickHouseDbTypeNameToClientTypeResolver](/class/clickhouse/clickhousedbtypenametoclienttyperesolver). |

## Methods

| Name | Description |
|:-----|:------------|
| GetFields / GetFieldsAsync | Returns the list of [DbField](/class/dbfield) objects for the given table, sourced from `system.columns`. |
| GetScopeIdentity / GetScopeIdentityAsync | Always throws `NotSupportedException` — ClickHouse has no session-wide scope identity, sequence, or auto-increment mechanism. |

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new ClickHouseDbHelper(new MyCustomClickHouseDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<ClickHouseConnection>(dbHelper, true);
```
