---
layout: default
sidebar: classes
title: "MariaDbGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the MySqlConnector-based MariaDb data provider."
permalink: /class/mariadb/mariadbconnector/mariadbglobalconfiguration
tags: [repodb, mariadbglobalconfiguration, mariadbconnector]
parent: "MariaDbConnector"
grand_parent: CLASSES
nav_order: 2
---

# MariaDbGlobalConfiguration

---

This class exposes the `UseMariaDbConnector()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for the `MySqlConnector`-based [RepoDb.MariaDbConnector](https://www.nuget.org/packages/RepoDb.MariaDbConnector) package (via [MariaDbBootstrap](/class/mariadb/mariadbconnector/mariadbbootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseMariaDbConnector();
```

{: .note }
> Call this once during application start-up, before opening any `MariaDbConnection`. This is unrelated to [MariaDbGlobalConfiguration.UseMariaDb()](/class/mariadb/mariadb/mariadbglobalconfiguration), which wires up the separate `MySql.Data`-based [RepoDb.MariaDb](https://www.nuget.org/packages/RepoDb.MariaDb) package instead.
