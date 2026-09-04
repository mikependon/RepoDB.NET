---
layout: default
sidebar: classes
title: "ClientTypeToEDBDbTypeResolver"
description: "A class used to resolve a .NET CLR type into its equivalent EDBType."
permalink: /class/enterprisedb/clienttypetoedbdbtyperesolver
tags: [repodb, clienttypetoedbdbtyperesolver, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# ClientTypeToEDBDbTypeResolver

---

This [IResolver](/interface/iresolver)`<Type, EDBType?>` implementation converts a .NET CLR type into its equivalent `RepoDb.Connector.EnterpriseDb.EDBType` (e.g. `typeof(Guid)` → `EDBType.Uuid`, `typeof(int)` → `EDBType.Integer`, `typeof(byte[])` → `EDBType.Bytea`).

{: .note }
> `EDBType` has no combinable "Array" member, so this resolver cannot represent array types — array-valued parameters are instead inferred directly by the Npgsql-backed driver at bind time, without going through this resolver at all (see [EnterpriseDbDbHelper](/class/enterprisedb/enterprisedbdbhelper)). Likewise, there is no CLR representation for PostgreSQL-specific geometric types, ranges, `pg_lsn`, or `tid` on this connector.

An unresolvable type throws an `InvalidOperationException`.

## Usability

```csharp
var resolver = new ClientTypeToEDBDbTypeResolver();
var edbType = resolver.Resolve(typeof(int)); // EDBType.Integer
```
