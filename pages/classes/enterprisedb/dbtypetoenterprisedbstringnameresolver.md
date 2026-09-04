---
layout: default
sidebar: classes
title: "DbTypeToEnterpriseDbStringNameResolver"
description: "A class used to resolve a DbType into its equivalent EnterpriseDB database string name."
permalink: /class/enterprisedb/dbtypetoenterprisedbstringnameresolver
tags: [repodb, dbtypetoenterprisedbstringnameresolver, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# DbTypeToEnterpriseDbStringNameResolver

---

This [IResolver](/interface/iresolver)`<DbType, string>` implementation converts a .NET `DbType` into its equivalent EnterpriseDB SQL type name (e.g. `DbType.String` → `TEXT`, `DbType.Int32` → `INTEGER`, `DbType.Binary`/`DbType.Byte` → `BYTEA`). It is used internally by [EnterpriseDbConvertFieldResolver](/class/enterprisedb/enterprisedbconvertfieldresolver) and [EnterpriseDbStatementBuilder](/class/enterprisedb/enterprisedbstatementbuilder) to build `CAST(...)` expressions and key-returning clauses. An unrecognized `DbType` falls back to `TEXT`.

## Usability

```csharp
var resolver = new DbTypeToEnterpriseDbStringNameResolver();
var typeName = resolver.Resolve(DbType.Int64); // "BIGINT"
```
