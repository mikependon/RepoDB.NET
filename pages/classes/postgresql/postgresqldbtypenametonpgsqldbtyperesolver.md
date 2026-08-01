---
layout: default
sidebar: classes
title: "PostgreSqlDbTypeNameToNpgsqlDbTypeResolver"
description: "A class used to resolve a PostgreSql database type name into its equivalent NpgsqlDbType."
permalink: /class/postgresql/postgresqldbtypenametonpgsqldbtyperesolver
tags: [repodb, postgresqldbtypenametonpgsqldbtyperesolver, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlDbTypeNameToNpgsqlDbTypeResolver

---

This [IResolver](/interface/iresolver)`<string, NpgsqlDbType?>` implementation converts a PostgreSQL database type name into its equivalent [NpgsqlDbType](https://www.npgsql.org/doc/api/NpgsqlTypes.NpgsqlDbType.html). It resolves in three steps:

1. Tries to parse the name directly as an `NpgsqlDbType` enum member.
2. Returns `NpgsqlDbType.Unknown` for the reflection-reported `USER-DEFINED` type (e.g. a native PostgreSQL enum type).
3. Otherwise, falls back to [PostgreSqlDbTypeNameToClientTypeResolver](/class/postgresql/postgresqldbtypenametoclienttyperesolver) to get the .NET CLR type, then [ClientTypeToNpgsqlDbTypeResolver](/class/postgresql/clienttypetonpgsqldbtyperesolver) to resolve that type into its `NpgsqlDbType`.

It is used by the PostgreSQL bulk operations (`RepoDb.PostgreSql.BulkOperations`) to determine the `NpgsqlDbType` of a column from its database type name when writing via the binary importer.

## Usability

```csharp
var resolver = new PostgreSqlDbTypeNameToNpgsqlDbTypeResolver();
var npgsqlDbType = resolver.Resolve("uuid"); // NpgsqlDbType.Uuid
```
