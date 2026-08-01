---
layout: default
sidebar: classes
title: "OracleDbTypeNameToClientTypeResolver"
description: "A class used to resolve an Oracle database type name into its equivalent .NET CLR type."
permalink: /class/oracle/oracledbtypenametoclienttyperesolver
tags: [repodb, oracledbtypenametoclienttyperesolver, oracle]
parent: "Oracle"
grand_parent: CLASSES
---

# OracleDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts an Oracle column data type name — as returned by `ALL_TAB_COLUMNS.DATA_TYPE` — into its equivalent .NET CLR type (e.g. `VARCHAR2` → `string`, `NUMBER` → `decimal`, `BLOB`/`RAW` → `byte[]`). It also handles the `TIMESTAMP(n) [WITH [LOCAL] TIME ZONE]` and `INTERVAL ... TO ...` qualifier forms. It is the default `DbTypeResolver` used by [OracleDbHelper](/class/oracle/oracledbhelper).

## Usability

```csharp
var resolver = new OracleDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("VARCHAR2"); // typeof(string)
```
