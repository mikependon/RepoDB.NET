---
layout: default
sidebar: classes
title: "MariaDbDbTypeNameToClientTypeResolver"
description: "A class used to resolve a MySql.Data-based MariaDb database type name into its equivalent .NET CLR type."
permalink: /class/mariadb/mariadb/mariadbdbtypenametoclienttyperesolver
tags: [repodb, mariadbdbtypenametoclienttyperesolver, mariadb]
parent: "MariaDb"
grand_parent: "MariaDB"
nav_order: 6
---

# MariaDbDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a MariaDB column data type name — as returned by `INFORMATION_SCHEMA.COLUMNS.DATA_TYPE` — into its equivalent .NET CLR type (e.g. `varchar`/`text` → `string`, `int` → `int`, `bit` → `ulong`, `blob`/`binary`/`geometry` → `byte[]`). It is the default `DbTypeResolver` used by [MariaDbDbHelper](/class/mariadb/mariadb/mariadbdbhelper).

## Usability

```csharp
var resolver = new MariaDbDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("varchar"); // typeof(string)
```
