---
layout: default
sidebar: classes
title: "EnterpriseDbDbTypeNameToEDBDbTypeResolver"
description: "A class used to resolve an EnterpriseDB database type name into its equivalent EDBDbType."
permalink: /class/enterprisedb/enterprisedbdbtypenametoedbdbtyperesolver
tags: [repodb, enterprisedbdbtypenametoedbdbtyperesolver, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbDbTypeNameToEDBDbTypeResolver

---

This [IResolver](/interface/iresolver)`<string, EDBDbType?>` implementation converts an EnterpriseDB database type name into its equivalent `EDBTypes.EDBDbType`. It resolves in three steps:

1. Tries to parse the name directly as an `EDBDbType` enum member.
2. Returns `EDBDbType.Unknown` for the reflection-reported `USER-DEFINED` type (e.g. a native enum type).
3. Otherwise, falls back to [EnterpriseDbDbTypeNameToClientTypeResolver](/class/enterprisedb/enterprisedbdbtypenametoclienttyperesolver) to get the .NET CLR type, then [ClientTypeToEDBDbTypeResolver](/class/enterprisedb/clienttypetoedbdbtyperesolver) to resolve that type into its `EDBDbType`.

## Usability

```csharp
var resolver = new EnterpriseDbDbTypeNameToEDBDbTypeResolver();
var edbDbType = resolver.Resolve("uuid"); // EDBDbType.Uuid
```
