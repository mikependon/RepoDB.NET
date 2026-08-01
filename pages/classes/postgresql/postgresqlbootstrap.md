---
layout: default
sidebar: classes
title: "PostgreSqlBootstrap"
description: "A class that is being used to initialize the necessary settings for the NpgsqlConnection object."
permalink: /class/postgresql/postgresqlbootstrap
tags: [repodb, postgresqlbootstrap, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlBootstrap

---

This class initializes the necessary dependencies for the `NpgsqlConnection` object — the [DbSetting](/class/postgresql/postgresqldbsetting), [DbHelper](/class/postgresql/postgresqldbhelper) and [StatementBuilder](/class/postgresql/postgresqlstatementbuilder) — and registers them via their respective mappers.

{: .note }
> The static `Initialize()` method is obsolete. Use [PostgreSqlGlobalConfiguration.UsePostgreSql()](/class/postgresql/postgresqlglobalconfiguration) instead.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [PostgreSqlGlobalConfiguration.UsePostgreSql()](/class/postgresql/postgresqlglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UsePostgreSql();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UsePostgreSql()` more than once has no additional effect.
