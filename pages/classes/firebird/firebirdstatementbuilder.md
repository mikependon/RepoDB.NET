---
layout: default
sidebar: classes
title: "FirebirdStatementBuilder"
description: "A class used to build the SQL statements for Firebird 3.0 and later."
permalink: /class/firebird/firebirdstatementbuilder
tags: [repodb, firebirdstatementbuilder, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for Firebird. It targets Firebird 3.0 and later, and is automatically registered by [FirebirdBootstrap](/class/firebird/firebirdbootstrap) — you do not need to instantiate it directly under normal use.

## Constructors

```csharp
public FirebirdStatementBuilder()
public FirebirdStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

`convertFieldResolver` defaults to [FirebirdConvertFieldResolver](/class/firebird/firebirdconvertfieldresolver), used to render `CAST(...)` expressions for typed fields.

## Firebird-specific generation notes

- Paging uses `FIRST n` (top-N [Query](/operation/query)/[Exists](/operation/exists)) and `FIRST m SKIP n` (for [BatchQuery](/operation/batchquery)/skip-take queries), written directly after `SELECT` — Firebird has no `TOP`/`LIMIT` keyword.
- [Insert](/operation/insert) excludes the identity column from the column list (letting `GENERATED ALWAYS`/`BY DEFAULT AS IDENTITY` auto-populate it) and appends a `RETURNING <key> AS "Result"` clause, which Firebird's `RETURNING` on `INSERT` natively surfaces as an ordinary single-row result set — no PL/SQL block or output-parameter wrapping is required, unlike Oracle.
- [InsertAll](/operation/insertall) reuses the single-row [Insert](/operation/insert) statement (`batchSize` of `1`); a `batchSize` greater than `1` throws `NotSupportedException`, since `FirebirdDbSetting.IsMultiStatementExecutable` is `false`.
- [Merge](/operation/merge) compiles to Firebird's native `UPDATE OR INSERT INTO ... MATCHING (...) RETURNING ...` upsert statement — the closest single-statement shape to MySQL's `ON DUPLICATE KEY UPDATE`/PostgreSQL's `ON CONFLICT DO UPDATE`. When the identity column is itself a qualifier, `UPDATE OR INSERT`'s `MATCHING` cannot reliably match a not-yet-inserted row, so the builder instead emits an `EXECUTE BLOCK` (Firebird's inline PL/SQL construct) that branches: `INSERT` when the identity parameter is `NULL`/`0`, otherwise `UPDATE OR INSERT`, returning the key either way via a `SUSPEND`-ed output parameter.
- [MergeAll](/operation/mergeall) reuses the single-row [Merge](/operation/merge) statement the same way [InsertAll](/operation/insertall) does, with the same `batchSize` restriction.
- [Truncate](/operation/truncate) compiles to a plain `DELETE FROM t` — Firebird has no `TRUNCATE TABLE` statement (as of 5.0) — which, unlike a real truncate, does not reset a `GENERATED ... AS IDENTITY` column's next value.
- Every generated statement omits the trailing `;` that [BaseStatementBuilder](/class/basestatementbuilder) normally appends, since Firebird's DSQL layer rejects a trailing statement terminator on a statement submitted through `FbCommand.CommandText`.
- Passing a non-null `hints` argument to any `Create*` method throws `NotSupportedException`, since `FirebirdDbSetting.AreTableHintsSupported` is `false`.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(FbConnection), new MyCustomFirebirdStatementBuilder(dbSetting), true);
```
