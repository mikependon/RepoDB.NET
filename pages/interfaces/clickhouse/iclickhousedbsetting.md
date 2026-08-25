---
layout: default
sidebar: interfaces
title: "IClickHouseDbSetting"
permalink: /interface/clickhouse/iclickhousedbsetting
tags: [repodb, iclickhousedbsetting, clickhouse]
parent: "ClickHouse"
grand_parent: INTERFACES
---

# IClickHouseDbSetting

---

This interface extends [IDbSetting](/interface/idbsetting) with a ClickHouse-specific setting. It is implemented by [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting), the default database setting registered for `ClickHouseConnection`.

{: .note }
> `IsWaitForMutationsEnabled` is only consulted by `BulkMerge`/`BulkUpdate`/`BulkDelete` in [RepoDb.ClickHouse.BulkOperations](/release/clickhousebulk); the plain `Update`/`UpdateAll`/`Delete`/`DeleteAll` operations never read it.

## Properties

Below is the list of properties, in addition to the ones inherited from [IDbSetting](/interface/idbsetting).

| Name | Description |
|:-----|:------------|
| IsWaitForMutationsEnabled | Gets or sets a value indicating whether `BulkMerge`/`BulkUpdate`/`BulkDelete` (from [RepoDb.ClickHouse.BulkOperations](/release/clickhousebulk)) block until their `ALTER TABLE` mutation completes. Has no effect on the plain [Update](/operation/update)/[UpdateAll](/operation/updateall)/[Delete](/operation/delete)/[DeleteAll](/operation/deleteall) operations. |

## How to Implement?

You have to manually create a class that implements this interface (typically by deriving from [BaseDbSetting](/class/basedbsetting), the same way [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting) does).

```csharp
public class MyCustomClickHouseDbSetting : BaseDbSetting, IClickHouseDbSetting
{
    public MyCustomClickHouseDbSetting()
    {
        AreTableHintsSupported = false;
        ClosingQuote = "`";
        OpeningQuote = "`";
        ParameterPrefix = string.Empty;
    }

    public bool IsWaitForMutationsEnabled { get; set; } = true;
}
```

## Usability

Once the class has been implemented, call [DbSettingMapper](/mapper/dbsettingmapper) to map it against `ClickHouseConnection`.

```csharp
DbSettingMapper.Add(typeof(ClickHouseConnection), new MyCustomClickHouseDbSetting(), true);
```
