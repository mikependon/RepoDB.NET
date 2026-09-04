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

This class extends [SapHanaDbSetting](/class/saphana/saphanadbsetting) with a `WriteToServerExecution` property that controls how the `Async` overloads of the [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) operations write rows to the server.

Unlike [SapHanaDbSetting](/class/saphana/saphanadbsetting), it is **not** registered automatically — [SapHanaBootstrap](/class/saphana/saphanabootstrap) maps the base [SapHanaDbSetting](/class/saphana/saphanadbsetting) by default. Register this class yourself via [DbSettingMapper](/mapper/dbsettingmapper) only if you need to override the default write strategy.

## Properties

| Name | Description |
|:-----|:------------|
| WriteToServerExecution | A [SapHanaWriteToServerExecution](/enumeration/saphana/saphanawritetoserverexecution) value that controls how the bulk operations' `Async` overloads write rows to the server. Defaults to `SapHanaCommandBatcher`. |

## Usability

Register it in place of the default [SapHanaDbSetting](/class/saphana/saphanadbsetting) to opt into the `AsyncOverSync` write strategy.

```csharp
DbSettingMapper.Add<HanaConnection>(
    new SapHanaBulkDbSetting { WriteToServerExecution = SapHanaWriteToServerExecution.AsyncOverSync },
    true);
```

{: .note }
> Register this after [UseSapHana()](/class/saphana/saphanaconfiguration) so it overrides the [SapHanaDbSetting](/class/saphana/saphanadbsetting) mapping that call sets up.
