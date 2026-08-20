---
layout: default
sidebar: classes
title: "ClickHouseStatementBuilder"
description: "A class used to build the SQL statements for ClickHouse."
permalink: /class/clickhouse/clickhousestatementbuilder
tags: [repodb, clickhousestatementbuilder, clickhouse]
parent: "ClickHouse"
grand_parent: CLASSES
---

# ClickHouseStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for ClickHouse. It is automatically registered by [ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap) — you do not need to instantiate it directly under normal use.

It generates backtick (`` ` ``)-quoted, `@`-parameterized SQL. `CreateMax`/`CreateMaxAll`/`CreateMin`/`CreateMinAll`/`CreateSum`/`CreateSumAll`/`CreateCount`/`CreateCountAll` are not overridden — the base class's SQL is valid ClickHouse SQL as-is.

`CreateInsert`/`CreateInsertAll` throw `NotSupportedException` when the entity has an identity field — ClickHouse has no identity/auto-increment columns, so a key value must always be supplied by the caller.

`CreateUpdate`/`CreateUpdateAll` and `CreateExists` (via a `WHERE`-filtered mutation) compile to `ALTER TABLE table UPDATE col = expr [, ...] WHERE filter` — an asynchronous *mutation* applied by background merges, not immediately, with a mandatory `WHERE` clause.

`CreateMerge`/`CreateMergeAll` deliberately emit the same plain `INSERT` as `CreateInsert`/`CreateInsertAll` rather than hard-failing or faking a real merge — true de-duplication is deferred to the table engine (e.g. `ReplacingMergeTree`) and its background merges, which is the idiomatic ClickHouse upsert pattern.

{: .note }
> For a real, immediate matched/unmatched merge (an `ALTER TABLE ... UPDATE` for matched rows plus an anti-join `INSERT` for unmatched ones), use [BulkMerge](/operation/clickhouse/bulkmerge) from [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations) instead.

`CreateBatchQuery`/`CreateSkipQuery` use `LIMIT`/`LIMIT ... OFFSET` for paging.

## Constructors

```csharp
public ClickHouseStatementBuilder()
public ClickHouseStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

The parameterless constructor resolves `dbSetting` via `DbSettingMapper.Get<ClickHouseConnection>()`, so it can only be used after [ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap) has run.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(ClickHouseConnection), new MyCustomClickHouseStatementBuilder(dbSetting), true);
```
