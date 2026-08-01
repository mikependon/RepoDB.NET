---
layout: default
sidebar: classes
title: "SqlServerStatementBuilder"
description: "A class used to build the SQL statements for SQL Server. This is the default statement builder used by the library."
permalink: /class/sqlserver/sqlserverstatementbuilder
tags: [repodb, sqlserverstatementbuilder, sqlserver]
parent: "SQL Server"
grand_parent: CLASSES
---

# SqlServerStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for SQL Server, and is the default statement builder used by the library. It is automatically registered by [SqlServerBootstrap](/class/sqlserver/sqlserverbootstrap) — you do not need to instantiate it directly under normal use.

It generates `[schema].[table]`-quoted, `@`-parameterized T-SQL, and honors the `hints` argument (e.g. `WITH (NOLOCK)`) on every operation that accepts one — see [SqlServerTableHints](/class/sqlserver/sqlservertablehints).

For [Insert](/operation/insert), [InsertAll](/operation/insertall), [Merge](/operation/merge) and [MergeAll](/operation/mergeall), the newly generated identity or primary key value is returned via `SCOPE_IDENTITY()` (single-row `Insert`) or an `OUTPUT` clause routed through a table variable (`InsertAll`/`Merge`/`MergeAll`) — the indirection through a table variable avoids SQL Server's restriction against an `OUTPUT` clause on a target table that has enabled triggers.

[BatchQuery](/operation/batchquery) and the internal skip/take query are built using a `ROW_NUMBER()` CTE rather than `OFFSET`/`FETCH`, so paging also works reliably against older compatibility levels.

## Constructors

```csharp
public SqlServerStatementBuilder(IDbSetting dbSetting)
public SqlServerStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

`convertFieldResolver` defaults to [SqlServerConvertFieldResolver](/class/sqlserver/sqlserverconvertfieldresolver), used to render `CONVERT(...)` expressions for typed fields.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(SqlConnection), new MyCustomSqlServerStatementBuilder(dbSetting), true);
```
