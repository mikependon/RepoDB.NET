---
layout: default
sidebar: classes
title: "DbTypeToFirebirdStringNameResolver"
description: "A class used to resolve a DbType into its equivalent Firebird database string name."
permalink: /class/firebird/dbtypetofirebirdstringnameresolver
tags: [repodb, dbtypetofirebirdstringnameresolver, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# DbTypeToFirebirdStringNameResolver

---

This [IResolver](/interface/iresolver)`<DbType, string>` implementation converts a .NET `DbType` into its equivalent Firebird SQL type name (e.g. `DbType.String` → `VARCHAR(8191)`, `DbType.Guid` → `CHAR(16) CHARACTER SET OCTETS`, `DbType.Binary` → `BLOB SUB_TYPE 0`). It is used internally by [FirebirdConvertFieldResolver](/class/firebird/firebirdconvertfieldresolver) to build `CAST(...)` expressions.

## Usability

```csharp
var resolver = new DbTypeToFirebirdStringNameResolver();
var typeName = resolver.Resolve(DbType.Int64); // "BIGINT"
```
