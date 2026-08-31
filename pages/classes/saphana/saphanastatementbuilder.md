---
layout: default
sidebar: classes
title: "SapHanaStatementBuilder"
description: "A class used to build the SQL statements for SAP HANA."
permalink: /class/saphana/saphanastatementbuilder
tags: [repodb, saphanastatementbuilder, saphana]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for SAP HANA. It is automatically registered by [SapHanaBootstrap](/class/saphana/saphanabootstrap) — you do not need to instantiate it directly under normal use.

## Constructors

```csharp
public SapHanaStatementBuilder()
public SapHanaStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

{: .note }
> The parameterless constructor passes `null` for both `convertFieldResolver` and `averageableClientTypeResolver` — SAP HANA has no `ConvertFieldResolver` class of its own, so [BaseStatementBuilder](/class/basestatementbuilder)'s own defaults are used instead. This differs from Db2, Firebird, Oracle, PostgreSQL, and Vertica, each of which supplies a provider-specific resolver here.

## SAP HANA-specific generation notes

- Paging uses `LIMIT`/`LIMIT ... OFFSET` for [Query](/operation/query)/top-N queries and [BatchQuery](/operation/batchquery)/skip-take queries (the ANSI `LIMIT take OFFSET skip` form, not MySQL's comma-separated `LIMIT skip, take`).
- [Insert](/operation/insert) is left as the plain `INSERT` [BaseStatementBuilder](/class/basestatementbuilder) already produces — unlike the SQL-Server-family pattern of appending a trailing `SELECT ... AS "Result"` to hand a generated key back in the same round trip, SAP HANA's ADO.NET client rejects a command text containing more than one statement. The generated/identity key is instead fetched via a separate [GetScopeIdentity](/class/saphana/saphanadbhelper) round trip; a non-identity primary key is simply read back off the entity the caller already supplied it on.
- [InsertAll](/operation/insertall)/[MergeAll](/operation/mergeall) reuse the single-row [Insert](/operation/insert)/[Merge](/operation/merge) statement — `IsMultiStatementExecutable` being `false` means RepoDb.Core always forces `batchSize` down to `1` before either is called.
- [Merge](/operation/merge) compiles to SAP HANA's native `UPSERT ... WITH PRIMARY KEY` statement. For the same reason as [Insert](/operation/insert), no trailing key-returning clause is appended — but this isn't a functional loss here, since `UPSERT ... WITH PRIMARY KEY` requires the primary key value up front to match against, so it's already known on the entity being merged.
- `COUNT`/`MAX`/`MIN`/`SUM` post-process [BaseStatementBuilder](/class/basestatementbuilder)'s output to collapse `"COUNT ("`/`"MAX ("`/`"MIN ("`/`"SUM ("` (with a space before the opening parenthesis) down to `"COUNT("`/`"MAX("`/`"MIN("`/`"SUM("`, matching SAP HANA's own SQL formatting conventions.
- [Truncate](/operation/truncate) is not overridden — SAP HANA has genuine `TRUNCATE TABLE` support, unlike Firebird and Vertica.
- Passing a non-null `hints` argument to any `Create*` method throws `NotSupportedException`, since `SapHanaDbSetting.AreTableHintsSupported` is `false`.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(HanaConnection), new MyCustomSapHanaStatementBuilder(dbSetting), true);
```
