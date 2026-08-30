---
layout: default
sidebar: classes
title: "VerticaDbTypeNameToClientTypeResolver"
description: "A class used to resolve a Vertica database type name into its equivalent .NET CLR type."
permalink: /class/vertica/verticadbtypenametoclienttyperesolver
tags: [repodb, verticadbtypenametoclienttyperesolver, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a Vertica column type name — as returned by `v_catalog.columns.data_type`, with its `(size)`/`(precision,scale)` suffix already stripped — into its equivalent .NET CLR type. It is the default `DbTypeResolver` used by [VerticaDbHelper](/class/vertica/verticadbhelper).

{: .note }
> Verified directly against `VerticaDataReader.GetSchemaTable()`: Vertica has no distinct storage widths for its integer or floating-point types — `SMALLINT`/`INTEGER`/`BIGINT`/etc. are all synonyms for one 8-byte integer (reported as `int`), and `FLOAT`/`DOUBLE PRECISION`/`REAL` are all synonyms for one 8-byte float (reported as `float`) — so both resolve to their widest CLR type (`long`/`double`), not `int`/`float`. `TIME` is reported back as `System.DateTime`, not `TimeSpan`.

## Usability

```csharp
var resolver = new VerticaDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("varchar"); // typeof(string)
```
