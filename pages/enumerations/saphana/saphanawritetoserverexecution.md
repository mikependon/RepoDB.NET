---
layout: default
sidebar: enumerations
title: "SapHanaWriteToServerExecution"
description: "An enumeration that is being used to define the behavior of the bulk-import write when executing towards the underlying target table."
permalink: /enumeration/saphana/saphanawritetoserverexecution
tags: [repodb, saphanawritetoserverexecution]
parent: "SAP HANA"
grand_parent: ENUMERATIONS
---

# SapHanaWriteToServerExecution

---

This enum defines how the `Async` overloads of the [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) operations write rows to the server. It is read from [SapHanaBulkDbSetting.WriteToServerExecution](/class/saphana/saphanabulkdbsetting), which is not registered by default.

## Enum Values

| Name | Description |
|:-----|:------------|
| SapHanaCommandBatcher | Writes through [SapHanaCommandBatcher](/class/saphana/saphanacommandbatcher), a `HanaCommand`-based row batcher, giving genuine asynchronous execution — `HanaBulkCopy` itself has no native async API. This is the default. |
| AsyncOverSync | Writes through the same synchronous `HanaBulkCopy`-based path the sync overloads use, called from within the `Async` overload rather than through [SapHanaCommandBatcher](/class/saphana/saphanacommandbatcher). |

{: .note }
> The sync overloads always use `HanaBulkCopy` directly, regardless of this setting — it only affects the `Async` overloads.

## Usability

Set the value on a [SapHanaBulkDbSetting](/class/saphana/saphanabulkdbsetting) instance and register it via [DbSettingMapper](/mapper/dbsettingmapper).

```csharp
DbSettingMapper.Add<HanaConnection>(
    new SapHanaBulkDbSetting { WriteToServerExecution = SapHanaWriteToServerExecution.AsyncOverSync },
    true);
```
