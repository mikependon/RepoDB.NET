---
layout: default
sidebar: classes
title: "SdsSqLiteDbTypeNameToClientTypeResolver"
description: "A class used to resolve a SQLite database type name into its equivalent .NET CLR type, for the System.Data.SQLite.Core driver."
permalink: /class/sqlite/sdssqlitedbtypenametoclienttyperesolver
tags: [repodb, sdssqlitedbtypenametoclienttyperesolver, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# SdsSqLiteDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a SQLite column data type name into its equivalent .NET CLR type, specifically for the [System.Data.SQLite.Core](https://www.nuget.org/packages/System.Data.SQLite.Core) driver ("Sds"). It is not wired up automatically by [SqliteBootstrap](/class/sqlite/sqlitebootstrap) — that class defaults to [MdsSqLiteDbTypeNameToClientTypeResolver](/class/sqlite/mdssqlitedbtypenametoclienttyperesolver) for `Microsoft.Data.Sqlite`. Supply this resolver explicitly when your application connects through `System.Data.SQLite.Core` instead.

Reflecting how `System.Data.SQLite.Core` reads back SQLite columns with stronger typing, `date`/`datetime`/`time` resolve to `DateTime`, `boolean` resolves to `long`, and `decimal`/`numeric` resolve to `decimal` — all different from [MdsSqLiteDbTypeNameToClientTypeResolver](/class/sqlite/mdssqlitedbtypenametoclienttyperesolver)'s mappings for those same type names.

## Usability

```csharp
var dbSetting = new SqLiteDbSetting();
var dbHelper = new SqLiteDbHelper(dbSetting, new SdsSqLiteDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<SqliteConnection>(dbHelper, true);
```
