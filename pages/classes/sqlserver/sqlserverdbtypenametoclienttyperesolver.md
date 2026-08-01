---
layout: default
sidebar: classes
title: "SqlServerDbTypeNameToClientTypeResolver"
description: "A class used to resolve a SQL Server database type name into its equivalent .NET CLR type."
permalink: /class/sqlserver/sqlserverdbtypenametoclienttyperesolver
tags: [repodb, sqlserverdbtypenametoclienttyperesolver, sqlserver]
parent: "SQL Server"
grand_parent: CLASSES
---

# SqlServerDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a SQL Server column data type name — as returned by `INFORMATION_SCHEMA.COLUMNS.DATA_TYPE` — into its equivalent .NET CLR type (e.g. `varchar`/`nvarchar` → `string`, `int` → `int`, `uniqueidentifier` → `Guid`, `binary`/`varbinary`/`timestamp` → `byte[]`). `sql_variant` resolves to the library's own `SqlVariant` marker type. It is the default `DbTypeResolver` used by [SqlServerDbHelper](/class/sqlserver/sqlserverdbhelper).

## Usability

```csharp
var resolver = new SqlServerDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("varchar"); // typeof(string)
```
