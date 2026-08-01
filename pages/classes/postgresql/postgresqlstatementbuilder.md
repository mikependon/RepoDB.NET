---
layout: default
sidebar: classes
title: "PostgreSqlStatementBuilder"
description: "A class used to build the SQL statements for PostgreSql."
permalink: /class/postgresql/postgresqlstatementbuilder
tags: [repodb, postgresqlstatementbuilder, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for PostgreSQL. It is automatically registered by [PostgreSqlBootstrap](/class/postgresql/postgresqlbootstrap) — you do not need to instantiate it directly under normal use.

It generates `"schema"."table"`-quoted, `@`-parameterized SQL, and uses `LIMIT`/`OFFSET` for [BatchQuery](/operation/batchquery) and the internal skip/take query rather than a windowed CTE.

For [Insert](/operation/insert), [InsertAll](/operation/insertall), [Merge](/operation/merge) and [MergeAll](/operation/mergeall), the newly generated identity or primary key value is returned via a `RETURNING` clause. [Merge](/operation/merge)/[MergeAll](/operation/mergeall) are compiled as `INSERT ... ON CONFLICT (qualifiers) DO UPDATE SET ...`, adding `OVERRIDING SYSTEM VALUE` whenever an identity field is present so an explicit value can still be supplied for a `GENERATED ALWAYS AS IDENTITY` column.

{: .note }
> `InsertAll` composes a single multi-row `INSERT ... VALUES (...), (...), ...` statement rather than delegating to the base class's sub-query form — PostgreSQL rejects the latter for this operation ([issue #1143](https://github.com/mikependon/RepoDB/issues/1143)).

[Truncate](/operation/truncate) is compiled as `TRUNCATE TABLE ... RESTART IDENTITY`, resetting any identity sequence back to its start value.

## Constructors

```csharp
public PostgreSqlStatementBuilder()
public PostgreSqlStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

The parameterless constructor resolves `dbSetting` via `DbSettingMapper.Get<NpgsqlConnection>()`, so it can only be used after [PostgreSqlBootstrap](/class/postgresql/postgresqlbootstrap) has run. `convertFieldResolver` defaults to [PostgreSqlConvertFieldResolver](/class/postgresql/postgresqlconvertfieldresolver), used to render `CAST(...)` expressions for typed fields.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(NpgsqlConnection), new MyCustomPostgreSqlStatementBuilder(dbSetting), true);
```
