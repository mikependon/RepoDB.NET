---
layout: default
sidebar: classes
title: "MariaDbBootstrap"
description: "A class that is being used to initialize the necessary settings for the MariaDbConnection (MySqlConnector-based) object."
permalink: /class/mariadb/mariadbconnector/mariadbbootstrap
tags: [repodb, mariadbbootstrap, mariadbconnector]
parent: "MariaDbConnector"
grand_parent: "MariaDB"
nav_order: 1
---

# MariaDbBootstrap

---

This class initializes the necessary dependencies for the `MySqlConnector`-based `MariaDbConnection` object — the [DbSetting](/class/mariadb/mariadbconnector/mariadbdbsetting), [DbHelper](/class/mariadb/mariadbconnector/mariadbdbhelper) and [StatementBuilder](/class/mariadb/mariadbconnector/mariadbstatementbuilder) — and registers them via their respective mappers.

{: .note }
> There is no public `Initialize()` method on this class. Use [MariaDbGlobalConfiguration.UseMariaDbConnector()](/class/mariadb/mariadbconnector/mariadbglobalconfiguration) instead.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [MariaDbGlobalConfiguration.UseMariaDbConnector()](/class/mariadb/mariadbconnector/mariadbglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseMariaDbConnector();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseMariaDbConnector()` more than once has no additional effect.
