---
layout: default
sidebar: classes
title: "MySqlBootstrap"
description: "A class that is being used to initialize the necessary settings for the MySqlConnection (MySql.Data) object."
permalink: /class/mysql/mysqldata/mysqlbootstrap
tags: [repodb, mysqlbootstrap, mysql]
parent: "MySql"
grand_parent: CLASSES
nav_order: 1
---

# MySqlBootstrap

---

This class initializes the necessary dependencies for the [MySql.Data](https://www.nuget.org/packages/MySql.Data) `MySqlConnection` object — the [DbSetting](/class/mysql/mysqldata/mysqldbsetting), [DbHelper](/class/mysql/mysqldata/mysqldbhelper) and [StatementBuilder](/class/mysql/mysqldata/mysqlstatementbuilder) — and registers them via their respective mappers.

{: .note }
> The static `Initialize()` method is obsolete. Use [MySqlGlobalConfiguration.UseMySql()](/class/mysql/mysqldata/mysqlglobalconfiguration) instead.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [MySqlGlobalConfiguration.UseMySql()](/class/mysql/mysqldata/mysqlglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseMySql();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseMySql()` more than once has no additional effect.
