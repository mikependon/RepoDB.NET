---
layout: default
sidebar: classes
title: "ClientTypeToNpgsqlDbTypeResolver"
description: "A class used to resolve a .NET CLR type into its equivalent NpgsqlDbType."
permalink: /class/postgresql/clienttypetonpgsqldbtyperesolver
tags: [repodb, clienttypetonpgsqldbtyperesolver, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# ClientTypeToNpgsqlDbTypeResolver

---

This [IResolver](/interface/iresolver)`<Type, NpgsqlDbType?>` implementation converts a .NET CLR type into its equivalent [NpgsqlDbType](https://www.npgsql.org/doc/api/NpgsqlTypes.NpgsqlDbType.html) (e.g. `Guid` → `Uuid`, `Int32` → `Integer`, `byte[]` → `Bytea`), including the Npgsql geometric and network-address types (e.g. `NpgsqlPoint` → `Point`, `IPAddress` → `Inet`, `PhysicalAddress` → `MacAddr`). It throws `InvalidOperationException` for a type it cannot resolve.

It is used by the PostgreSQL bulk operations (`RepoDb.PostgreSql.BulkOperations`) to determine the `NpgsqlDbType` of a column when writing via the binary importer.

## Usability

```csharp
var resolver = new ClientTypeToNpgsqlDbTypeResolver();
var npgsqlDbType = resolver.Resolve(typeof(Guid)); // NpgsqlDbType.Uuid
```
