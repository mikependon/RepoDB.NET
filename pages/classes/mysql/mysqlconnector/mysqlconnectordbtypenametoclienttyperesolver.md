---
layout: default
sidebar: classes
title: "MySqlConnectorDbTypeNameToClientTypeResolver"
description: "A class used to resolve a MySqlConnector database type name into its equivalent .NET CLR type."
permalink: /class/mysql/mysqlconnector/mysqlconnectordbtypenametoclienttyperesolver
tags: [repodb, mysqlconnectordbtypenametoclienttyperesolver, mysql, mysqlconnector]
parent: "MySQL"
grand_parent: CLASSES
nav_order: 13
---

# MySqlConnectorDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a MySQL column data type name — as returned by `INFORMATION_SCHEMA.COLUMNS.DATA_TYPE` — into its equivalent .NET CLR type (e.g. `varchar`/`text` → `string`, `int` → `int`, `bit` → `ulong`, `blob`/`binary`/`geometry` → `byte[]`). It is the default `DbTypeResolver` used by [MySqlConnectorDbHelper](/class/mysql/mysqlconnector/mysqlconnectordbhelper), and resolves identically to [MySqlDbTypeNameToClientTypeResolver](/class/mysql/mysqldata/mysqldbtypenametoclienttyperesolver) (MySql.Data).

## Usability

```csharp
var resolver = new MySqlConnectorDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("varchar"); // typeof(string)
```
