---
layout: default
sidebar: classes
title: "SapHanaDbTypeToStringNameResolver"
description: "A class used to resolve a HanaDbType into its equivalent database string name."
permalink: /class/saphana/saphanadbtypetostringnameresolver
tags: [repodb, saphanadbtypetostringnameresolver, saphana]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaDbTypeToStringNameResolver

---

This [IResolver](/interface/iresolver)`<HanaDbType, string>` implementation converts a `Sap.Data.Hana.HanaDbType` value into its equivalent SAP HANA SQL type name (e.g. `HanaDbType.NVarChar` → `NVARCHAR`, `HanaDbType.SmallDecimal` → `SMALLDECIMAL`). An unrecognized value falls back to `TEXT`.

{: .note }
> The source file for this class is named `SapHanaDbTypeToSapHanaStringNameResolver.cs`, but the class itself is `SapHanaDbTypeToStringNameResolver` (no repeated "SapHana") — likely a naming inconsistency left over from authoring. This page documents the actual class name.

## Usability

```csharp
var resolver = new SapHanaDbTypeToStringNameResolver();
var typeName = resolver.Resolve(HanaDbType.BigInt); // "BIGINT"
```
