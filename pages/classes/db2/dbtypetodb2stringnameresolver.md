---
layout: default
sidebar: classes
title: "DbTypeToDb2StringNameResolver"
description: "A class used to resolve a DbType into its equivalent Db2 database string name."
permalink: /class/db2/dbtypetodb2stringnameresolver
tags: [repodb, dbtypetodb2stringnameresolver, db2]
parent: "Db2"
grand_parent: CLASSES
---

# DbTypeToDb2StringNameResolver

---

This [IResolver](/interface/iresolver)`<DbType, string>` implementation converts a .NET `DbType` into its equivalent Db2 SQL type name (e.g. `DbType.String` → `VARCHAR(2000)`, `DbType.Guid` → `CHAR(16) FOR BIT DATA`). It is used internally by [Db2ConvertFieldResolver](/class/db2/db2convertfieldresolver) to build `CAST(...)` expressions.

## Usability

```csharp
var resolver = new DbTypeToDb2StringNameResolver();
var typeName = resolver.Resolve(DbType.Int64); // "BIGINT"
```
