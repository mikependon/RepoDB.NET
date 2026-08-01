---
layout: default
sidebar: classes
title: "DbTypeToSqLiteStringNameResolver"
description: "A class used to resolve a DbType into its equivalent SQLite database string name."
permalink: /class/sqlite/dbtypetosqlitestringnameresolver
tags: [repodb, dbtypetosqlitestringnameresolver, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# DbTypeToSqLiteStringNameResolver

---

This [IResolver](/interface/iresolver)`<DbType, string>` implementation converts a .NET `DbType` into its equivalent SQLite SQL type name (e.g. `DbType.String` → `TEXT`, `DbType.Int32`/`DbType.Int16` → `INT`, `DbType.Byte`/`DbType.Binary` → `BLOB`). It is used internally by [SqLiteConvertFieldResolver](/class/sqlite/sqliteconvertfieldresolver) to build `CAST(...)` expressions, and by [SqLiteStatementBuilder](/class/sqlite/sqlitestatementbuilder) to type the `Result` column returned by [Insert](/operation/insert)/[InsertAll](/operation/insertall).

## Usability

```csharp
var resolver = new DbTypeToSqLiteStringNameResolver();
var typeName = resolver.Resolve(DbType.Int64); // "BIGINT"
```
