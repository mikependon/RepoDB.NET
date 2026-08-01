---
layout: default
sidebar: classes
title: "SqlServerGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the SQL Server data provider."
permalink: /class/sqlserver/sqlserverglobalconfiguration
tags: [repodb, sqlserverglobalconfiguration, sqlserver]
parent: "SQL Server"
grand_parent: CLASSES
---

# SqlServerGlobalConfiguration

---

This class exposes the `UseSqlServer()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for SQL Server (via [SqlServerBootstrap](/class/sqlserver/sqlserverbootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseSqlServer();
```

{: .note }
> Call this once during application start-up, before opening any `SqlConnection`.
