---
layout: default
sidebar: classes
title: "SqliteBootstrap"
description: "A class that is being used to initialize the necessary settings for the SqliteConnection object."
permalink: /class/sqlite/sqlitebootstrap
tags: [repodb, sqlitebootstrap, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# SqliteBootstrap

---

This class initializes the necessary dependencies for the [Microsoft.Data.Sqlite](https://www.nuget.org/packages/Microsoft.Data.Sqlite) `SqliteConnection` object — the [DbSetting](/class/sqlite/sqlitedbsetting), [DbHelper](/class/sqlite/sqlitedbhelper) and [StatementBuilder](/class/sqlite/sqlitestatementbuilder) — and registers them via their respective mappers.

It wires up [MdsSqLiteDbTypeNameToClientTypeResolver](/class/sqlite/mdssqlitedbtypenametoclienttyperesolver) as the type resolver, since this package targets `Microsoft.Data.Sqlite` (the "Mds" driver).

{: .note }
> The static `Initialize()` method is obsolete. Use [SqliteGlobalConfiguration.UseSqlite()](/class/sqlite/sqliteglobalconfiguration) instead.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [SqliteGlobalConfiguration.UseSqlite()](/class/sqlite/sqliteglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseSqlite();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseSqlite()` more than once has no additional effect.
