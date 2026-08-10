---
layout: default
sidebar: classes
title: "Db2StatementBuilder"
description: "A class used to build the SQL statements for Db2 Database 10.5 and later."
permalink: /class/db2/db2statementbuilder
tags: [repodb, db2statementbuilder, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2StatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for Db2. It targets Db2 for Linux, UNIX, and Windows (LUW) 10.5 and later, and is automatically registered by [Db2Bootstrap](/class/db2/db2bootstrap) — you do not need to instantiate it directly under normal use.

## Constructors

```csharp
public Db2StatementBuilder(IDbSetting dbSetting)
public Db2StatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

`convertFieldResolver` defaults to [Db2ConvertFieldResolver](/class/db2/db2convertfieldresolver), used to render `CAST(...)` expressions for typed fields.

## Db2-specific generation notes

- Paging uses `OFFSET ... ROWS FETCH NEXT ... ROWS ONLY` for [BatchQuery](/operation/batchquery)/skip-take queries, and a trailing `FETCH FIRST n ROWS ONLY` for [Exists](/operation/exists) and top-N [Query](/operation/query) — Db2 has no `TOP` keyword.
- [Insert](/operation/insert)/[InsertAll](/operation/insertall) read back a generated key via `SELECT <key> FROM FINAL TABLE (INSERT INTO ...)`, an ANSI-adjacent construct that surfaces the post-insert row as an ordinary result set.
- [Merge](/operation/merge)/[MergeAll](/operation/mergeall) are generated as `MERGE INTO t USING (SELECT ... FROM SYSIBM.SYSDUMMY1) s ON (...) WHEN MATCHED THEN UPDATE ... WHEN NOT MATCHED THEN INSERT ...`, since Db2 has no native upsert statement equivalent. `SYSIBM.SYSDUMMY1` (Db2's single-row dummy table) is required because `MERGE`'s `USING` source needs a `FROM` even when only selecting bind variables. Because Db2 LUW's `MERGE` does not support `FINAL TABLE`, the generated key is instead retrieved via a follow-up `SELECT` appended to the same command text.
- [Truncate](/operation/truncate) always appends the `IMMEDIATE` keyword (`TRUNCATE TABLE t IMMEDIATE`), which older Db2 versions (pre-11.5 Mod Pack 2) require.
- Every generated statement omits the trailing `;` that [BaseStatementBuilder](/class/basestatementbuilder) normally appends, since the IBM Data Server .NET Provider rejects a trailing statement terminator on a plain (non-compound-statement) command.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(DB2Connection), new MyCustomDb2StatementBuilder(dbSetting), true);
```
