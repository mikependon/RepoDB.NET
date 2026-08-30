---
layout: default
sidebar: classes
title: "VerticaConfiguration"
description: "A class that is being used to initialize the necessary settings for the Vertica data provider."
permalink: /class/vertica/verticaconfiguration
tags: [repodb, verticaconfiguration, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaConfiguration

---

This class exposes the `UseVertica()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for Vertica (via [VerticaBootstrap](/class/vertica/verticabootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseVertica();
```

{: .note }
> Call this once during application start-up, before opening any `VerticaConnection`.
