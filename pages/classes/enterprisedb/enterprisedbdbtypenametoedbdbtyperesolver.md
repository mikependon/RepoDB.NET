---
layout: default
sidebar: classes
title: "EnterpriseDbDbTypeNameToEDBDbTypeResolver"
description: "A class used to resolve an EnterpriseDB database type name into its equivalent EDBType."
permalink: /class/enterprisedb/enterprisedbdbtypenametoedbdbtyperesolver
tags: [repodb, enterprisedbdbtypenametoedbdbtyperesolver, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbDbTypeNameToEDBDbTypeResolver

---

This [IResolver](/interface/iresolver)`<string, EDBType?>` implementation converts an EnterpriseDB database type name into its equivalent `RepoDb.Connector.EnterpriseDb.EDBType`. It resolves in three steps:

1. Tries to parse the name directly as an `EDBType` enum member.
2. Returns `null` for the reflection-reported `USER-DEFINED` type (e.g. a native enum type) — `EDBType` has no `Unknown` member to fall back to.
3. Otherwise, falls back to [EnterpriseDbDbTypeNameToClientTypeResolver](/class/enterprisedb/enterprisedbdbtypenametoclienttyperesolver) to get the .NET CLR type, then [ClientTypeToEDBDbTypeResolver](/class/enterprisedb/clienttypetoedbdbtyperesolver) to resolve that type into its `EDBType` — returning `null` instead of throwing if that final step can't resolve it either.

## Usability

```csharp
var resolver = new EnterpriseDbDbTypeNameToEDBDbTypeResolver();
var edbType = resolver.Resolve("uuid"); // EDBType.Uuid
```
