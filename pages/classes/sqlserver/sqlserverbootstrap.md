---
layout: default
sidebar: classes
title: "SqlServerBootstrap"
description: "A class that is being used to initialize the necessary settings for the SqlConnection object."
permalink: /class/sqlserver/sqlserverbootstrap
tags: [repodb, sqlserverbootstrap, sqlserver]
parent: "SQL Server"
grand_parent: CLASSES
---

# SqlServerBootstrap

---

This class initializes the necessary dependencies for the `SqlConnection` object — the [DbSetting](/class/sqlserver/sqlserverdbsetting), [DbHelper](/class/sqlserver/sqlserverdbhelper) and [StatementBuilder](/class/sqlserver/sqlserverstatementbuilder) — and registers them via their respective mappers.

{: .note }
> The static `Initialize()` method is obsolete. Use [SqlServerGlobalConfiguration.UseSqlServer()](/class/sqlserver/sqlserverglobalconfiguration) instead.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [SqlServerGlobalConfiguration.UseSqlServer()](/class/sqlserver/sqlserverglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseSqlServer();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseSqlServer()` more than once has no additional effect.
