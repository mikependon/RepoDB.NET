---
layout: default
sidebar: classes
title: "MdsSqLiteDbTypeNameToClientTypeResolver"
description: "A class used to resolve a SQLite database type name into its equivalent .NET CLR type, for the Microsoft.Data.Sqlite driver."
permalink: /class/sqlite/mdssqlitedbtypenametoclienttyperesolver
tags: [repodb, mdssqlitedbtypenametoclienttyperesolver, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# MdsSqLiteDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a SQLite column data type name into its equivalent .NET CLR type, specifically for the [Microsoft.Data.Sqlite](https://www.nuget.org/packages/Microsoft.Data.Sqlite) driver ("Mds"). It is the default `DbTypeResolver` used by [SqLiteDbHelper](/class/sqlite/sqlitedbhelper) when initialized via [SqliteBootstrap](/class/sqlite/sqlitebootstrap).

Reflecting how `Microsoft.Data.Sqlite` reads back loosely-typed SQLite columns, `boolean`, `char`, `date`, `datetime`, `none`, `string`, `text`, `time` and `varchar` all resolve to `string`, while `bigint`, `decimal`, `int`, `integer` and `numeric` all resolve to `long`.

{: .important }
> Compare this with [SdsSqLiteDbTypeNameToClientTypeResolver](/class/sqlite/sdssqlitedbtypenametoclienttyperesolver) — for the `System.Data.SQLite.Core` driver, `date`/`datetime`/`time` resolve to `DateTime`, `boolean` resolves to `long`, and `decimal`/`numeric` resolve to `decimal`. Using the wrong one of the two against a given driver will misrepresent these column types.

## Usability

```csharp
var resolver = new MdsSqLiteDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("text"); // typeof(string)
```
