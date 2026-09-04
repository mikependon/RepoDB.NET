---
layout: default
sidebar: classes
title: "EnterpriseDbStatementBuilder"
description: "A class used to build the SQL statements for EnterpriseDB (EDB Postgres Advanced Server)."
permalink: /class/enterprisedb/enterprisedbstatementbuilder
tags: [repodb, enterprisedbstatementbuilder, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for EnterpriseDB. It is automatically registered by [EnterpriseDbBootstrap](/class/enterprisedb/enterprisedbbootstrap) — you do not need to instantiate it directly under normal use.

## Constructors

```csharp
public EnterpriseDbStatementBuilder()
public EnterpriseDbStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

The parameterless constructor defaults `convertFieldResolver` to [EnterpriseDbConvertFieldResolver](/class/enterprisedb/enterprisedbconvertfieldresolver).

## EnterpriseDB-specific generation notes

- Paging uses `LIMIT`/`LIMIT ... OFFSET` for [Query](/operation/query)/top-N queries and [BatchQuery](/operation/batchquery)/skip-take queries.
- [Insert](/operation/insert) appends a trailing `RETURNING ... AS "Result"` clause so the generated/primary key is returned in the same round trip.
- [InsertAll](/operation/insertall) is a plain multi-row `INSERT ... VALUES (...), (...), ...` — no `RETURNING` clause, since EDB Postgres rejects a `RETURNING` list against a sub-table VALUES expression (see [issue #1143](https://github.com/mikependon/RepoDB/issues/1143)).
- [Merge](/operation/merge)/[MergeAll](/operation/mergeall) compile to `INSERT ... ON CONFLICT (qualifiers) DO UPDATE SET ...`, each with a trailing `RETURNING` clause (`MergeAll` also returns a per-row `__RepoDb_OrderColumn` alongside the key, to align results back to the source rows). An identity column is inserted with `OVERRIDING SYSTEM VALUE` so an explicit value on the entity is respected instead of silently ignored.
- [Truncate](/operation/truncate) appends `RESTART IDENTITY`.
- Passing a non-null `hints` argument to any `Create*` method throws `NotSupportedException`, since `EnterpriseDbDbSetting.AreTableHintsSupported` is `false`.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(EDBConnection), new MyCustomEnterpriseDbStatementBuilder(dbSetting), true);
```
