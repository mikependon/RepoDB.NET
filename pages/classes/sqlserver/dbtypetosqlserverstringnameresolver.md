---
layout: default
sidebar: classes
title: "DbTypeToSqlServerStringNameResolver"
description: "A class used to resolve a DbType into its equivalent SQL Server database string name."
permalink: /class/sqlserver/dbtypetosqlserverstringnameresolver
tags: [repodb, dbtypetosqlserverstringnameresolver, sqlserver]
parent: "SQL Server"
grand_parent: CLASSES
---

# DbTypeToSqlServerStringNameResolver

---

This [IResolver](/interface/iresolver)`<DbType, string>` implementation converts a .NET `DbType` into its equivalent SQL Server SQL type name (e.g. `DbType.String` → `NVARCHAR`, `DbType.Guid` → `UNIQUEIDENTIFIER`, `DbType.Decimal` → `DECIMAL(18,2)`). It is used internally by [SqlServerConvertFieldResolver](/class/sqlserver/sqlserverconvertfieldresolver) to build `CONVERT(...)` expressions.

## Usability

```csharp
var resolver = new DbTypeToSqlServerStringNameResolver();
var typeName = resolver.Resolve(DbType.Int64); // "BIGINT"
```
