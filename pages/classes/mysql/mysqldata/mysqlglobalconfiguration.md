---
layout: default
sidebar: classes
title: "MySqlGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the MySql.Data data provider."
permalink: /class/mysql/mysqldata/mysqlglobalconfiguration
tags: [repodb, mysqlglobalconfiguration, mysql]
parent: "MySql"
grand_parent: CLASSES
nav_order: 2
---

# MySqlGlobalConfiguration

---

This class exposes the `UseMySql()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for [MySql.Data](https://www.nuget.org/packages/MySql.Data) (via [MySqlBootstrap](/class/mysql/mysqldata/mysqlbootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseMySql();
```

{: .note }
> Call this once during application start-up, before opening any `MySqlConnection`.
