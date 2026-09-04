---
layout: default
sidebar: classes
title: "EnterpriseDbBootstrap"
description: "A class that is being used to initialize the necessary settings for the EDBConnection object."
permalink: /class/enterprisedb/enterprisedbbootstrap
tags: [repodb, enterprisedbbootstrap, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbBootstrap

---

This class initializes the necessary dependencies for `EDBConnection` — the [DbSetting](/class/enterprisedb/enterprisedbdbsetting), [DbHelper](/class/enterprisedb/enterprisedbdbhelper) and [StatementBuilder](/class/enterprisedb/enterprisedbstatementbuilder) — and registers them via their respective mappers.

{: .note }
> Registration happens against **both** connection types — the official `EnterpriseDB.EDBClient.EDBConnection` driver and the Npgsql-backed `RepoDb.Connector.EnterpriseDb.EDBConnection` connector — so either can be used with the core provider.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [EnterpriseDbGlobalConfiguration.UseEnterpriseDb()](/class/enterprisedb/enterprisedbglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseEnterpriseDb();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseEnterpriseDb()` more than once has no additional effect.
