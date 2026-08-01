---
layout: default
sidebar: classes
title: "SqLiteConvertFieldResolver"
description: "A class used to resolve the Field name conversion for SQLite."
permalink: /class/sqlite/sqliteconvertfieldresolver
tags: [repodb, sqliteconvertfieldresolver, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# SqLiteConvertFieldResolver

---

This class resolves a [Field](/class/field) into a `CAST(column AS "TYPE")` SQL fragment whenever the field carries an explicit `.Type`, using [DbTypeToSqLiteStringNameResolver](/class/sqlite/dbtypetosqlitestringnameresolver) to determine the SQLite type name. It is used internally by [SqLiteStatementBuilder](/class/sqlite/sqlitestatementbuilder) and is not typically used directly.

Unlike [SqlServerConvertFieldResolver](/class/sqlserver/sqlserverconvertfieldresolver) and [PostgreSqlConvertFieldResolver](/class/postgresql/postgresqlconvertfieldresolver), it does not override the base `DbConvertFieldResolver`'s `Resolve` method — it only supplies the SQLite-specific `dbTypeResolver`/`stringNameResolver` pair to it.

## Usability

```csharp
var resolver = new SqLiteConvertFieldResolver();
var expression = resolver.Resolve(new Field("Age", typeof(int)), dbSetting);
// CAST([Age] AS [INT])
```
