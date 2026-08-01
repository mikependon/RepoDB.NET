---
layout: default
sidebar: classes
title: "SqLiteStatementBuilder"
description: "A class used to build the SQL statements for SQLite."
permalink: /class/sqlite/sqlitestatementbuilder
tags: [repodb, sqlitestatementbuilder, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# SqLiteStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for SQLite. It is automatically registered by [SqliteBootstrap](/class/sqlite/sqlitebootstrap) — you do not need to instantiate it directly under normal use.

It generates `[table]`-quoted, `@`-parameterized SQL, and uses `LIMIT`/`OFFSET` for [BatchQuery](/operation/batchquery) and the internal skip/take query.

{: .important }
> `CreateMerge` and `CreateMergeAll` throw `NotImplementedException` — SQLite has no `MERGE` statement, and the `INSERT OR REPLACE` fallback for it exists in source only as commented-out code. This normally wouldn't matter, since [Merge](/operation/merge)/[MergeAll](/operation/mergeall) never reach these methods for SQLite: [SqLiteDbSetting](/class/sqlite/sqlitedbsetting) sets `IsUseUpsert` to `true`, which reroutes both operations through an `Exists`-then-`Insert`/`Update` flow instead. The exception only surfaces if you call `CreateMerge`/`CreateMergeAll` directly, or if a custom `IDbSetting` sets `IsUseUpsert` back to `false` for a `SqliteConnection`.

For [Insert](/operation/insert), the newly generated identity or primary key value is returned via `CAST(last_insert_rowid() AS ...)` (identity column) or the supplied parameter (non-identity primary key). [InsertAll](/operation/insertall) instead appends a SQLite `RETURNING` clause (requires SQLite 3.35+) to read back the generated value per batched row.

[Truncate](/operation/truncate) is compiled as `DELETE FROM [table] ; VACUUM`, since SQLite has no native `TRUNCATE TABLE` statement.

## Constructors

```csharp
public SqLiteStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

`convertFieldResolver` defaults to [SqLiteConvertFieldResolver](/class/sqlite/sqliteconvertfieldresolver), used to render `CAST(...)` expressions for typed fields.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(SqliteConnection), new MyCustomSqLiteStatementBuilder(dbSetting), true);
```
