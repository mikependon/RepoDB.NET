---
layout: default
sidebar: classes
title: "TypeToDb2TypeResolver"
description: "A class used to resolve a .NET CLR Type into its equivalent DB2Type."
permalink: /class/db2/typetodb2typeresolver
tags: [repodb, typetodb2typeresolver, db2]
parent: "Db2"
grand_parent: CLASSES
---

# TypeToDb2TypeResolver

---

This [IResolver](/interface/iresolver)`<Type, DB2Type>` implementation converts a .NET CLR `Type` into its equivalent `DB2Type` (e.g. `string` → `VarChar`, `DateTime` → `Timestamp`, `DateTimeOffset` → `TimeStampWithTimeZone`, `Guid` → `Binary`, `byte[]` → `Blob`). Nullable value types are unwrapped to their underlying type before resolution, and an unrecognized type falls back to `DB2Type.VarChar`.

It is used internally by [Db2BulkArrayBinder](/class/db2/db2bulkarraybinder) to infer the `DB2Type` of a bound parameter when a mapping does not specify one explicitly.

## Usability

```csharp
var resolver = new TypeToDb2TypeResolver();
var db2Type = resolver.Resolve(typeof(DateTime)); // DB2Type.Timestamp
```
