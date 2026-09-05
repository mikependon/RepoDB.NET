---
layout: default
sidebar: classes
title: "SapHanaBulkDbSetting"
description: "A setting class used to control the write strategy of the SAP HANA bulk operations."
permalink: /class/saphana/saphanabulkdbsetting
tags: [repodb, saphanabulkdbsetting, saphana, bulk]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaBulkDbSetting

---

This class extends [SapHanaDbSetting](/class/saphana/saphanadbsetting) and implements [ISapHanaBulkDbSetting](/interface/saphana/isaphanabulkdbsetting) with a `WriteToServerExecution` property that controls how the `Async` overloads of the [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) operations write rows to the server.

Unlike [SapHanaDbSetting](/class/saphana/saphanadbsetting), it is **not** registered automatically — [SapHanaBootstrap](/class/saphana/saphanabootstrap) maps the base [SapHanaDbSetting](/class/saphana/saphanadbsetting) by default. Register this class yourself only if you need to override the default write strategy.

## Properties

| Name | Description |
|:-----|:------------|
| WriteToServerExecution | A [SapHanaWriteToServerExecution](/enumeration/saphana/saphanawritetoserverexecution) value that controls how the bulk operations' `Async` overloads write rows to the server. Defaults to `SapHanaCommandBatcher`. |

## Usability

Pass an instance directly to `UseSapHana()` in place of the default settings object.

```csharp
GlobalConfiguration
    .Setup()
    .UseSapHana(new SapHanaBulkDbSetting { WriteToServerExecution = SapHanaWriteToServerExecution.AsyncOverSync });
```

Or register it after the fact via [DbSettingMapper](/mapper/dbsettingmapper).

```csharp
DbSettingMapper.Add<HanaConnection>(
    new SapHanaBulkDbSetting { WriteToServerExecution = SapHanaWriteToServerExecution.AsyncOverSync },
    true);
```

{: .note }
> This requires [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) to be installed, since that's where this class lives.
