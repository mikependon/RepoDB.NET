---
layout: default
sidebar: classes
title: "MariaDbBootstrap"
description: "A class that is being used to initialize the necessary settings for the MariaDbConnection (MySql.Data-based) object."
permalink: /class/mariadb/mariadb/mariadbbootstrap
tags: [repodb, mariadbbootstrap, mariadb]
parent: "MariaDb"
grand_parent: "MariaDB"
nav_order: 1
---

# MariaDbBootstrap

---

This class initializes the necessary dependencies for the `MySql.Data`-based `MariaDbConnection` object — the [DbSetting](/class/mariadb/mariadb/mariadbdbsetting), [DbHelper](/class/mariadb/mariadb/mariadbdbhelper) and [StatementBuilder](/class/mariadb/mariadb/mariadbstatementbuilder) — and registers them via their respective mappers.

{: .note }
> There is no public `Initialize()` method on this class. Use [MariaDbGlobalConfiguration.UseMariaDb()](/class/mariadb/mariadb/mariadbglobalconfiguration) instead.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [MariaDbGlobalConfiguration.UseMariaDb()](/class/mariadb/mariadb/mariadbglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseMariaDb();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseMariaDb()` more than once has no additional effect.
