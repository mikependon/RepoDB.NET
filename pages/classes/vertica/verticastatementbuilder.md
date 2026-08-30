---
layout: default
sidebar: classes
title: "VerticaStatementBuilder"
description: "A class used to build the SQL statements for Vertica."
permalink: /class/vertica/verticastatementbuilder
tags: [repodb, verticastatementbuilder, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for Vertica. It is automatically registered by [VerticaBootstrap](/class/vertica/verticabootstrap) — you do not need to instantiate it directly under normal use.

## Constructors

```csharp
public VerticaStatementBuilder()
public VerticaStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

`convertFieldResolver` defaults to [VerticaConvertFieldResolver](/class/vertica/verticaconvertfieldresolver), used to render `CAST(...)` expressions for typed fields.

## Vertica-specific generation notes

- Paging uses `LIMIT`/`LIMIT ... OFFSET` for [Query](/operation/query)/top-N queries and [BatchQuery](/operation/batchquery)/skip-take queries — no `TOP`/`FIRST` keyword needed.
- [InsertAll](/operation/insertall) generates a genuine multi-row `INSERT INTO ... VALUES (...), (...), ...` statement, since `VerticaDbSetting.IsInsertAllBatchable` is `true` even though `IsMultiStatementExecutable` is `false`.
- [Merge](/operation/merge)/[MergeAll](/operation/mergeall) never emit a native `MERGE` statement at all — Vertica rejects `MERGE` outright against any table with an `IDENTITY`/`AUTO_INCREMENT` column, and has no procedural fallback equivalent to Firebird's `EXECUTE BLOCK`. Instead, an `UPDATE ... WHERE qualifiers` is joined with a trailing `; INSERT ... WHERE NOT EXISTS (...)` into one command text, with a follow-up `SELECT LAST_INSERT_ID()` (or a `CASE WHEN <identity-param> IS NULL THEN LAST_INSERT_ID() ELSE <identity-param> END`, when the identity column is itself a qualifier) appended when a key needs to be returned.
- [Truncate](/operation/truncate) compiles to a plain `DELETE FROM t` — Vertica has no `TRUNCATE TABLE` statement (as of 5.0) — which does not reset an `IDENTITY` column's next value.
- Every generated statement omits the trailing `;` that [BaseStatementBuilder](/class/basestatementbuilder) normally appends, since Vertica's DSQL layer rejects a trailing statement terminator on a statement submitted through `VerticaCommand.CommandText`.
- Passing a non-null `hints` argument to any `Create*` method throws `NotSupportedException`, since `VerticaDbSetting.AreTableHintsSupported` is `false`.

{: .important }
> [Merge](/operation/merge)/[MergeAll](/operation/mergeall)'s `UPDATE ...; INSERT ...` command text is a compound, `;`-joined statement carrying parameters in both halves — this has not been verified against a live Vertica instance, and conflicts with the same compound-statement restriction documented elsewhere for `VerticaCommand` (see [Operations (Vertica)](/operation/vertica)). Verify this end-to-end before relying on [Merge](/operation/merge)/[MergeAll](/operation/mergeall) in production.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(VerticaConnection), new MyCustomVerticaStatementBuilder(dbSetting), true);
```
