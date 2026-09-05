---
layout: default
sidebar: interfaces
title: "ISapHanaBulkDbSetting"
permalink: /interface/saphana/isaphanabulkdbsetting
tags: [repodb, isaphanabulkdbsetting, saphana, bulk]
parent: "SAP HANA"
grand_parent: INTERFACES
---

# ISapHanaBulkDbSetting

---

This interface extends [IDbSetting](/interface/idbsetting) with a SAP HANA bulk-operations-specific setting. It ships in [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) — not the core [RepoDb.SapHana](https://www.nuget.org/packages/RepoDb.SapHana) package — and is implemented by [SapHanaBulkDbSetting](/class/saphana/saphanabulkdbsetting).

## Properties

Below is the list of properties, in addition to the ones inherited from [IDbSetting](/interface/idbsetting).

| Name | Description |
|:-----|:------------|
| WriteToServerExecution | A [SapHanaWriteToServerExecution](/enumeration/saphana/saphanawritetoserverexecution) value controlling how the bulk operations' `Async` overloads write rows to the server. |

{: .note }
> The internal check that consults this setting is written against the concrete [SapHanaBulkDbSetting](/class/saphana/saphanabulkdbsetting) type, not this interface — subclass [SapHanaBulkDbSetting](/class/saphana/saphanabulkdbsetting) rather than implementing this interface on an unrelated class if you need it honored.

## How to Implement?

Subclass [SapHanaBulkDbSetting](/class/saphana/saphanabulkdbsetting) itself, rather than implementing this interface from scratch.

```csharp
public class MySapHanaBulkDbSetting : SapHanaBulkDbSetting
{
    public MySapHanaBulkDbSetting()
    {
        WriteToServerExecution = SapHanaWriteToServerExecution.AsyncOverSync;
    }
}
```

## Usability

Pass it to `UseSapHana()`, or map it directly against `HanaConnection` via [DbSettingMapper](/mapper/dbsettingmapper).

```csharp
GlobalConfiguration
    .Setup()
    .UseSapHana(new MySapHanaBulkDbSetting());
```

```csharp
DbSettingMapper.Add(typeof(HanaConnection), new MySapHanaBulkDbSetting(), true);
```
