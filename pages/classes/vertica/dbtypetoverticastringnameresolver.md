---
layout: default
sidebar: classes
title: "DbTypeToVerticaStringNameResolver"
description: "A class used to resolve a DbType into its equivalent Vertica database string name."
permalink: /class/vertica/dbtypetoverticastringnameresolver
tags: [repodb, dbtypetoverticastringnameresolver, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# DbTypeToVerticaStringNameResolver

---

This [IResolver](/interface/iresolver)`<DbType, string>` implementation converts a .NET `DbType` into its equivalent Vertica SQL type name (e.g. `DbType.String` → `VARCHAR(8191)`, `DbType.Guid` → `UUID`, `DbType.Binary` → `VARBINARY(65000)`). It is used internally by [VerticaConvertFieldResolver](/class/vertica/verticaconvertfieldresolver) to build `CAST(...)` expressions.

## Usability

```csharp
var resolver = new DbTypeToVerticaStringNameResolver();
var typeName = resolver.Resolve(DbType.Int64); // "BIGINT"
```
