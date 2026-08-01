---
layout: default
sidebar: classes
title: "MySqlConnectorBootstrap"
description: "A class that is being used to initialize the necessary settings for the MySqlConnection (MySqlConnector) object."
permalink: /class/mysql/mysqlconnector/mysqlconnectorbootstrap
tags: [repodb, mysqlconnectorbootstrap, mysql, mysqlconnector]
parent: "MySQL"
grand_parent: CLASSES
nav_order: 8
---

# MySqlConnectorBootstrap

---

This class initializes the necessary dependencies for the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector) `MySqlConnection` object — the [DbSetting](/class/mysql/mysqlconnector/mysqlconnectordbsetting), [DbHelper](/class/mysql/mysqlconnector/mysqlconnectordbhelper) and [StatementBuilder](/class/mysql/mysqlconnector/mysqlconnectorstatementbuilder) — and registers them via their respective mappers.

{: .note }
> The static `Initialize()` method is obsolete. Use [MySqlConnectorConfiguration.UseMySqlConnector()](/class/mysql/mysqlconnector/mysqlconnectorconfiguration) instead.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [MySqlConnectorConfiguration.UseMySqlConnector()](/class/mysql/mysqlconnector/mysqlconnectorconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseMySqlConnector();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseMySqlConnector()` more than once has no additional effect.
