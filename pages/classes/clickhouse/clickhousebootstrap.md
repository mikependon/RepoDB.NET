---
layout: default
sidebar: classes
title: "ClickHouseBootstrap"
description: "A class that is being used to initialize the necessary settings for the ClickHouseConnection object."
permalink: /class/clickhouse/clickhousebootstrap
tags: [repodb, clickhousebootstrap, clickhouse]
parent: "ClickHouse"
grand_parent: CLASSES
---

# ClickHouseBootstrap

---

This class initializes the necessary dependencies for the `ClickHouseConnection` object — the [DbSetting](/class/clickhouse/clickhousedbsetting), [DbHelper](/class/clickhouse/clickhousedbhelper) and [StatementBuilder](/class/clickhouse/clickhousestatementbuilder) — and registers them via their respective mappers. The `DbSetting` registered defaults to a plain [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting), or whichever `IDbSetting` instance is passed to `UseClickHouse()`.

{: .note }
> There is no public `Initialize()` method on this class. Use [ClickHouseGlobalConfiguration.UseClickHouse()](/class/clickhouse/clickhouseglobalconfiguration) instead.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [ClickHouseGlobalConfiguration.UseClickHouse()](/class/clickhouse/clickhouseglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseClickHouse();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseClickHouse()` more than once has no additional effect.
