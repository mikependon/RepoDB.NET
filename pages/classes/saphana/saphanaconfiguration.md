---
layout: default
sidebar: classes
title: "SapHanaConfiguration"
description: "A class that is being used to initialize the necessary settings for the SAP HANA data provider."
permalink: /class/saphana/saphanaconfiguration
tags: [repodb, saphanaconfiguration, saphana]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaConfiguration

---

This class exposes the `UseSapHana()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for SAP HANA (via [SapHanaBootstrap](/class/saphana/saphanabootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseSapHana();
```

{: .note }
> Call this once during application start-up, before opening any `HanaConnection`.
