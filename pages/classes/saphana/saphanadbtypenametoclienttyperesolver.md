---
layout: default
sidebar: classes
title: "SapHanaDbTypeNameToClientTypeResolver"
description: "A class used to resolve a SAP HANA database type name into its equivalent .NET CLR type."
permalink: /class/saphana/saphanadbtypenametoclienttyperesolver
tags: [repodb, saphanadbtypenametoclienttyperesolver, saphana]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a SAP HANA column type name — as returned by `SYS.TABLE_COLUMNS.DATA_TYPE_NAME` — into its equivalent .NET CLR type (e.g. `VARCHAR`/`NVARCHAR`/`ALPHANUM`/`SHORTTEXT` → `string`, `DECIMAL`/`SMALLDECIMAL` → `decimal`, `BLOB`/`VARBINARY`/`BINARY`/`BINTEXT` → `byte[]`). It is the default `DbTypeResolver` used by [SapHanaDbHelper](/class/saphana/saphanadbhelper).

## Usability

```csharp
var resolver = new SapHanaDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("NVARCHAR"); // typeof(string)
```
