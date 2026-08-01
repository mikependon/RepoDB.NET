---
layout: default
sidebar: classes
title: "PostgreSqlDbTypeNameToClientTypeResolver"
description: "A class used to resolve a PostgreSql database type name into its equivalent .NET CLR type."
permalink: /class/postgresql/postgresqldbtypenametoclienttyperesolver
tags: [repodb, postgresqldbtypenametoclienttyperesolver, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a PostgreSQL column data type name — as returned by `information_schema.columns.data_type` — into its equivalent .NET CLR type (e.g. `character varying`/`text` → `string`, `integer` → `int`, `uuid` → `Guid`, `bytea` → `byte[]`). Geometric and range types resolve to their `NpgsqlTypes` equivalents (e.g. `point` → `NpgsqlPoint`, `numrange` → `NpgsqlRange<decimal>`). On .NET 6+, `date` and `time` resolve to `DateOnly`/`TimeOnly` instead of `DateTime`/`TimeSpan`. It is the default `DbTypeResolver` used by [PostgreSqlDbHelper](/class/postgresql/postgresqldbhelper).

## Usability

```csharp
var resolver = new PostgreSqlDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("character varying"); // typeof(string)
```
