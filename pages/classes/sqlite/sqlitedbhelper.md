---
layout: default
sidebar: classes
title: "SqLiteDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a SQLite table."
permalink: /class/sqlite/sqlitedbhelper
tags: [repodb, sqlitedbhelper, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# SqLiteDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for SQLite. It queries `pragma table_info(...)` to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements.

Because `pragma table_info` doesn't itself flag an autoincrement column, the identity column is instead detected by fetching the table's original `CREATE TABLE` text from `sqlite_master` and scanning its column definitions for the `AUTOINCREMENT` keyword, or an `INTEGER` column also marked `PRIMARY KEY` (SQLite's implicit `rowid` alias rule).

It also resolves the newly generated identity value via `SELECT last_insert_rowid()`.

Unlike the other providers' `DbHelper` classes, this one takes its `IDbSetting` as an explicit constructor argument rather than resolving it internally — this is what lets [SqliteBootstrap](/class/sqlite/sqlitebootstrap) wire it up against the [Microsoft.Data.Sqlite](https://www.nuget.org/packages/Microsoft.Data.Sqlite)-specific settings instance.

It is automatically registered by [SqliteBootstrap](/class/sqlite/sqlitebootstrap) — you do not need to instantiate it directly under normal use.

## Constructors

```csharp
public SqLiteDbHelper(IDbSetting dbSetting, IResolver<string, Type> dbTypeResolver)
```

## Properties

| Name | Description |
|:-----|:------------|
| DbSetting | The `IDbSetting` instance used by this helper. |
| DbTypeResolver | The `IResolver<string, Type>` used to convert a SQLite column data type (e.g. `TEXT`) into its equivalent .NET CLR type. [SqliteBootstrap](/class/sqlite/sqlitebootstrap) supplies [MdsSqLiteDbTypeNameToClientTypeResolver](/class/sqlite/mdssqlitedbtypenametoclienttyperesolver). |

## Usability

Only override this if you need a custom type resolver — for example, to swap in [SdsSqLiteDbTypeNameToClientTypeResolver](/class/sqlite/sdssqlitedbtypenametoclienttyperesolver) when connecting through `System.Data.SQLite.Core` instead of `Microsoft.Data.Sqlite`.

```csharp
var dbSetting = new SqLiteDbSetting();
var dbHelper = new SqLiteDbHelper(dbSetting, new SdsSqLiteDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<SqliteConnection>(dbHelper, true);
```
