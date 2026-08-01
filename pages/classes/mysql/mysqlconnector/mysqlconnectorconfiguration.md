---
layout: default
sidebar: classes
title: "MySqlConnectorConfiguration"
description: "A class that is being used to initialize the necessary settings for the MySqlConnector data provider."
permalink: /class/mysql/mysqlconnector/mysqlconnectorconfiguration
tags: [repodb, mysqlconnectorconfiguration, mysql, mysqlconnector]
parent: "MySQL"
grand_parent: CLASSES
nav_order: 9
---

# MySqlConnectorConfiguration

---

This class exposes the `UseMySqlConnector()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for [MySqlConnector](https://www.nuget.org/packages/MySqlConnector) (via [MySqlConnectorBootstrap](/class/mysql/mysqlconnector/mysqlconnectorbootstrap)).

{: .note }
> Unlike the other providers' equivalent class, this one is named `MySqlConnectorConfiguration` rather than `MySqlConnectorGlobalConfiguration`.

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseMySqlConnector();
```

{: .note }
> Call this once during application start-up, before opening any `MySqlConnection`.
