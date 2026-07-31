---
layout: default
sidebar: classes
title: "DbTypeToOracleStringNameResolver"
description: "A class used to resolve a DbType into its equivalent Oracle database string name."
permalink: /class/dbtypetooraclestringnameresolver
tags: [repodb, dbtypetooraclestringnameresolver, oracle]
parent: CLASSES
---

# DbTypeToOracleStringNameResolver

---

This [IResolver](/interface/iresolver)`<DbType, string>` implementation converts a .NET `DbType` into its equivalent Oracle SQL type name (e.g. `DbType.String` → `NVARCHAR2(2000)`, `DbType.Guid` → `RAW(16)`). It is used internally by [OracleConvertFieldResolver](/class/oracleconvertfieldresolver) to build `CAST(...)` expressions.

## Usability

```csharp
var resolver = new DbTypeToOracleStringNameResolver();
var typeName = resolver.Resolve(DbType.Int64); // "NUMBER(19)"
```
