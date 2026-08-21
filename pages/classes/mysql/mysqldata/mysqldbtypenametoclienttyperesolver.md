---
layout: default
sidebar: classes
title: "MySqlDbTypeNameToClientTypeResolver"
description: "A class used to resolve a MySql.Data database type name into its equivalent .NET CLR type."
permalink: /class/mysql/mysqldata/mysqldbtypenametoclienttyperesolver
tags: [repodb, mysqldbtypenametoclienttyperesolver, mysql]
parent: "MySql"
grand_parent: CLASSES
nav_order: 6
---

# MySqlDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a MySQL column data type name — as returned by `INFORMATION_SCHEMA.COLUMNS.DATA_TYPE` — into its equivalent .NET CLR type (e.g. `varchar`/`text` → `string`, `int` → `int`, `bit` → `ulong`, `blob`/`binary`/`geometry` → `byte[]`). It is the default `DbTypeResolver` used by [MySqlDbHelper](/class/mysql/mysqldata/mysqldbhelper).

## Usability

```csharp
var resolver = new MySqlDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("varchar"); // typeof(string)
```
