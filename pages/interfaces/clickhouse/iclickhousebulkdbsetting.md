---
layout: default
sidebar: interfaces
title: "IClickHouseBulkDbSetting"
permalink: /interface/clickhouse/iclickhousebulkdbsetting
tags: [repodb, iclickhousebulkdbsetting, clickhouse, bulk]
parent: "ClickHouse"
grand_parent: INTERFACES
---

# IClickHouseBulkDbSetting

---

This interface extends [IDbSetting](/interface/idbsetting) with a ClickHouse bulk-operations-specific setting. It ships in [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations) — not the core [RepoDb.ClickHouse](https://www.nuget.org/packages/RepoDb.ClickHouse) package — and is implemented by [ClickHouseBulkDbSetting](/class/clickhouse/clickhousebulkdbsetting).

{: .note }
> `IsWaitForMutationsEnabled` is only consulted by `BulkMerge`/`BulkUpdate`/`BulkDelete` in [RepoDb.ClickHouse.BulkOperations](/release/clickhousebulk); the plain `Update`/`UpdateAll`/`Delete`/`DeleteAll` operations never read it.

## Properties

Below is the list of properties, in addition to the ones inherited from [IDbSetting](/interface/idbsetting).

| Name | Description |
|:-----|:------------|
| IsWaitForMutationsEnabled | Gets or sets a value indicating whether `BulkMerge`/`BulkUpdate`/`BulkDelete` (from [RepoDb.ClickHouse.BulkOperations](/release/clickhousebulk)) block until their `ALTER TABLE` mutation completes. Has no effect on the plain [Update](/operation/update)/[UpdateAll](/operation/updateall)/[Delete](/operation/delete)/[DeleteAll](/operation/deleteall) operations. |

{: .note }
> The internal check that consults this setting is written against the concrete [ClickHouseBulkDbSetting](/class/clickhouse/clickhousebulkdbsetting) type, not this interface — subclass [ClickHouseBulkDbSetting](/class/clickhouse/clickhousebulkdbsetting) rather than implementing this interface on an unrelated class if you need it honored.

## How to Implement?

Subclass [ClickHouseBulkDbSetting](/class/clickhouse/clickhousebulkdbsetting) itself, rather than implementing this interface from scratch.

```csharp
public class MyClickHouseBulkDbSetting : ClickHouseBulkDbSetting
{
    public MyClickHouseBulkDbSetting()
    {
        IsWaitForMutationsEnabled = true;
    }
}
```

## Usability

Pass it to `UseClickHouse()`, or map it directly against `ClickHouseConnection` via [DbSettingMapper](/mapper/dbsettingmapper).

```csharp
GlobalConfiguration
    .Setup()
    .UseClickHouse(new MyClickHouseBulkDbSetting());
```

```csharp
DbSettingMapper.Add(typeof(ClickHouseConnection), new MyClickHouseBulkDbSetting(), true);
```
