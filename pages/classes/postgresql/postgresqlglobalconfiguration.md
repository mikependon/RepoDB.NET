---
layout: default
sidebar: classes
title: "PostgreSqlGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the PostgreSql data provider."
permalink: /class/postgresql/postgresqlglobalconfiguration
tags: [repodb, postgresqlglobalconfiguration, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlGlobalConfiguration

---

This class exposes the `UsePostgreSql()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for PostgreSQL (via [PostgreSqlBootstrap](/class/postgresql/postgresqlbootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UsePostgreSql();
```

{: .note }
> Call this once during application start-up, before opening any `NpgsqlConnection`.
