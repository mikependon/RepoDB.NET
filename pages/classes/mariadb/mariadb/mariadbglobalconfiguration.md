---
layout: default
sidebar: classes
title: "MariaDbGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the MySql.Data-based MariaDb data provider."
permalink: /class/mariadb/mariadb/mariadbglobalconfiguration
tags: [repodb, mariadbglobalconfiguration, mariadb]
parent: "MariaDb"
grand_parent: "MariaDB"
nav_order: 2
---

# MariaDbGlobalConfiguration

---

This class exposes the `UseMariaDb()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for the `MySql.Data`-based [RepoDb.MariaDb](https://www.nuget.org/packages/RepoDb.MariaDb) package (via [MariaDbBootstrap](/class/mariadb/mariadb/mariadbbootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseMariaDb();
```

{: .note }
> Call this once during application start-up, before opening any `MariaDbConnection`. This is unrelated to [MariaDbGlobalConfiguration.UseMariaDbConnector()](/class/mariadb/mariadbconnector/mariadbglobalconfiguration), which wires up the separate `MySqlConnector`-based [RepoDb.MariaDbConnector](https://www.nuget.org/packages/RepoDb.MariaDbConnector) package instead.
