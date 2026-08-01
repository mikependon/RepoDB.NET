---
layout: default
sidebar: classes
title: "OracleBootstrap"
description: "A class that is being used to initialize the necessary settings for the OracleConnection object."
permalink: /class/oracle/oraclebootstrap
tags: [repodb, oraclebootstrap, oracle]
parent: "Oracle"
grand_parent: CLASSES
---

# OracleBootstrap

---

This class initializes the necessary dependencies for the `OracleConnection` object — the [DbSetting](/class/oracle/oracledbsetting), [DbHelper](/class/oracle/oracledbhelper) and [StatementBuilder](/class/oracle/oraclestatementbuilder) — and registers them via their respective mappers.

It also forces `OracleConfiguration.BindByName = true`, since RepoDB's generated statements always bind parameters by name rather than position.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [OracleGlobalConfiguration.UseOracle()](/class/oracle/oracleglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseOracle();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseOracle()` more than once has no additional effect.
