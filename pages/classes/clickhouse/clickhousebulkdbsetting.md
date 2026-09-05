---
layout: default
sidebar: classes
title: "ClickHouseBulkDbSetting"
description: "A setting class used to control mutation-wait blocking for the ClickHouse bulk operations."
permalink: /class/clickhouse/clickhousebulkdbsetting
tags: [repodb, clickhousebulkdbsetting, clickhouse, bulk]
parent: "ClickHouse"
grand_parent: CLASSES
---

# ClickHouseBulkDbSetting

---

This class extends [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting) and implements [IClickHouseBulkDbSetting](/interface/clickhouse/iclickhousebulkdbsetting) with an `IsWaitForMutationsEnabled` property, consulted by `BulkMerge`/`BulkUpdate`/`BulkDelete` in [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations) to decide whether to block until their `ALTER TABLE` mutation completes.

Unlike [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting), it is **not** registered automatically — [ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap) maps the base [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting) by default. Register this class yourself only if you need mutation-wait blocking.

## Properties

| Name | Description |
|:-----|:------------|
| IsWaitForMutationsEnabled | Whether `BulkMerge`/`BulkUpdate`/`BulkDelete` block until their `ALTER TABLE` mutation completes. Defaults to `true` on this class, but has no effect unless this setting is registered in place of the plain [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting). |

{: .note }
> The plain [Update](/operation/update)/[UpdateAll](/operation/updateall)/[Delete](/operation/delete)/[DeleteAll](/operation/deleteall) operations never consult this setting and always return as soon as their mutation is queued.

## Usability

Pass an instance directly to `UseClickHouse()` in place of the default settings object.

```csharp
GlobalConfiguration
    .Setup()
    .UseClickHouse(new ClickHouseBulkDbSetting { IsWaitForMutationsEnabled = true });
```

{: .note }
> This requires [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations) to be installed, since that's where this class lives.
