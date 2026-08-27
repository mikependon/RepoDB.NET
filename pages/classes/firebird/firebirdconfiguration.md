---
layout: default
sidebar: classes
title: "FirebirdConfiguration"
description: "A class that is being used to initialize the necessary settings for the Firebird data provider."
permalink: /class/firebird/firebirdconfiguration
tags: [repodb, firebirdconfiguration, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdConfiguration

---

This class exposes the `UseFirebird()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for Firebird (via [FirebirdBootstrap](/class/firebird/firebirdbootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseFirebird();
```

{: .note }
> Call this once during application start-up, before opening any `FbConnection`.
