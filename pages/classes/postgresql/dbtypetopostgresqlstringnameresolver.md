---
layout: default
sidebar: classes
title: "DbTypeToPostgreSqlStringNameResolver"
description: "A class used to resolve a DbType into its equivalent PostgreSql database string name."
permalink: /class/postgresql/dbtypetopostgresqlstringnameresolver
tags: [repodb, dbtypetopostgresqlstringnameresolver, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# DbTypeToPostgreSqlStringNameResolver

---

This [IResolver](/interface/iresolver)`<DbType, string>` implementation converts a .NET `DbType` into its equivalent PostgreSQL SQL type name (e.g. `DbType.String` → `TEXT`, `DbType.Int32` → `INTEGER`, `DbType.Binary`/`DbType.Byte` → `BYTEA`). It is used internally by [PostgreSqlConvertFieldResolver](/class/postgresql/postgresqlconvertfieldresolver) to build `CAST(...)` expressions.

## Usability

```csharp
var resolver = new DbTypeToPostgreSqlStringNameResolver();
var typeName = resolver.Resolve(DbType.Int64); // "BIGINT"
```
