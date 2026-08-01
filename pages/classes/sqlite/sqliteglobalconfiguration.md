---
layout: default
sidebar: classes
title: "SqliteGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the SQLite data provider."
permalink: /class/sqlite/sqliteglobalconfiguration
tags: [repodb, sqliteglobalconfiguration, sqlite]
parent: "SQLite"
grand_parent: CLASSES
---

# SqliteGlobalConfiguration

---

This class exposes the `UseSqlite()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for SQLite (via [SqliteBootstrap](/class/sqlite/sqlitebootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseSqlite();
```

{: .note }
> Call this once during application start-up, before opening any `SqliteConnection`.
