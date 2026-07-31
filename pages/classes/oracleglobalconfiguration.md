---
layout: default
sidebar: classes
title: "OracleGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the Oracle data provider."
permalink: /class/oracleglobalconfiguration
tags: [repodb, oracleglobalconfiguration, oracle]
parent: CLASSES
---

# OracleGlobalConfiguration

---

This class exposes the `UseOracle()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for Oracle (via [OracleBootstrap](/class/oraclebootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseOracle();
```

{: .note }
> Call this once during application start-up, before opening any `OracleConnection`.
