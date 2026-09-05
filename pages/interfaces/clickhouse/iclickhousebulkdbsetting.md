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

## How to Implement?

You have to manually create a class that implements this interface (typically by deriving from [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting), the same way [ClickHouseBulkDbSetting](/class/clickhouse/clickhousebulkdbsetting) does).

```csharp
public class MyCustomClickHouseBulkDbSetting : ClickHouseDbSetting, IClickHouseBulkDbSetting
{
    public bool IsWaitForMutationsEnabled { get; set; } = true;
}
```

## Usability

Once the class has been implemented, pass it to `UseClickHouse()`, or map it directly against `ClickHouseConnection` via [DbSettingMapper](/mapper/dbsettingmapper).

```csharp
GlobalConfiguration
    .Setup()
    .UseClickHouse(new MyCustomClickHouseBulkDbSetting());
```

```csharp
DbSettingMapper.Add(typeof(ClickHouseConnection), new MyCustomClickHouseBulkDbSetting(), true);
```
