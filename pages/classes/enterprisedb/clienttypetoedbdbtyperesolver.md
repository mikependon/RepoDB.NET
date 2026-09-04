---
layout: default
sidebar: classes
title: "ClientTypeToEDBDbTypeResolver"
description: "A class used to resolve a .NET CLR type into its equivalent EDBDbType."
permalink: /class/enterprisedb/clienttypetoedbdbtyperesolver
tags: [repodb, clienttypetoedbdbtyperesolver, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# ClientTypeToEDBDbTypeResolver

---

This [IResolver](/interface/iresolver)`<Type, EDBDbType?>` implementation converts a .NET CLR type into its equivalent `EDBTypes.EDBDbType` (e.g. `typeof(Guid)` → `EDBDbType.Uuid`, `typeof(int)` → `EDBDbType.Integer`, `typeof(byte[])` → `EDBDbType.Bytea`). Array types are resolved by combining `EDBDbType.Array` with the element type's own resolved `EDBDbType`.

It also underlies [EnterpriseDbDbHelper](/class/enterprisedb/enterprisedbdbhelper)'s post-creation handling of array-valued parameters, where it sets `EDBParameter.EDBDbType` for a bound array value.

An unresolvable type throws an `InvalidOperationException`.

## Usability

```csharp
var resolver = new ClientTypeToEDBDbTypeResolver();
var edbDbType = resolver.Resolve(typeof(int)); // EDBDbType.Integer
```
