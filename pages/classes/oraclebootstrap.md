---
layout: default
sidebar: classes
title: "OracleBootstrap"
description: "A class that is being used to initialize the necessary settings for the OracleConnection object."
permalink: /class/oraclebootstrap
tags: [repodb, oraclebootstrap, oracle]
parent: CLASSES
---

# OracleBootstrap

---

This class initializes the necessary dependencies for the `OracleConnection` object — the [DbSetting](/class/oracledbsetting), [DbHelper](/class/oracledbhelper) and [StatementBuilder](/class/oraclestatementbuilder) — and registers them via their respective mappers.

It also forces `OracleConfiguration.BindByName = true`, since RepoDB's generated statements always bind parameters by name rather than position.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [OracleGlobalConfiguration.UseOracle()](/class/oracleglobalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseOracle();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseOracle()` more than once has no additional effect.
