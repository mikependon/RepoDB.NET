---
layout: default
sidebar: classes
title: "ClickHouseGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the ClickHouse data provider."
permalink: /class/clickhouse/clickhouseglobalconfiguration
tags: [repodb, clickhouseglobalconfiguration, clickhouse]
parent: "ClickHouse"
grand_parent: CLASSES
---

# ClickHouseGlobalConfiguration

---

This class exposes the `UseClickHouse()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for ClickHouse (via [ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseClickHouse();
```

An overload accepts a custom `IDbSetting`, e.g. [ClickHouseBulkDbSetting](/class/clickhouse/clickhousebulkdbsetting) in place of the default [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting).

```csharp
GlobalConfiguration
    .Setup()
    .UseClickHouse(new ClickHouseBulkDbSetting { IsWaitForMutationsEnabled = true });
```

{: .note }
> Call this once during application start-up, before opening any `ClickHouseConnection`.
