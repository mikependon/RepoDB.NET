---
layout: default
sidebar: classes
title: "TypeToOracleDbTypeResolver"
description: "A class used to resolve a .NET CLR Type into its equivalent OracleDbType."
permalink: /class/oracle/typetooracledbtyperesolver
tags: [repodb, typetooracledbtyperesolver, oracle]
parent: "Oracle"
grand_parent: CLASSES
---

# TypeToOracleDbTypeResolver

---

This [IResolver](/interface/iresolver)`<Type, OracleDbType>` implementation converts a .NET CLR `Type` into its equivalent `OracleDbType` (e.g. `string` → `NVarchar2`, `DateTime` → `TimeStamp`, `Guid` → `Raw`, `byte[]` → `Blob`). Nullable value types are unwrapped to their underlying type before resolution, and an unrecognized type falls back to `OracleDbType.NVarchar2`.

It is used internally by [OracleBulkArrayBinder](/class/oracle/oraclebulkarraybinder) to infer the `OracleDbType` of a bound parameter when a mapping does not specify one explicitly.

## Usability

```csharp
var resolver = new TypeToOracleDbTypeResolver();
var oracleDbType = resolver.Resolve(typeof(DateTime)); // OracleDbType.TimeStamp
```
