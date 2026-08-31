---
layout: default
sidebar: classes
title: "SapHanaBootstrap"
description: "A class that is being used to initialize the necessary settings for the HanaConnection object."
permalink: /class/saphana/saphanabootstrap
tags: [repodb, saphanabootstrap, saphana]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaBootstrap

---

This class initializes the necessary dependencies for the `HanaConnection` object — the [DbSetting](/class/saphana/saphanadbsetting), [DbHelper](/class/saphana/saphanadbhelper) and [StatementBuilder](/class/saphana/saphanastatementbuilder) — and registers them via their respective mappers.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [SapHanaConfiguration.UseSapHana()](/class/saphana/saphanaconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseSapHana();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseSapHana()` more than once has no additional effect.
