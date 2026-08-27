---
layout: default
sidebar: classes
title: "FirebirdBootstrap"
description: "A class that is being used to initialize the necessary settings for the FbConnection object."
permalink: /class/firebird/firebirdbootstrap
tags: [repodb, firebirdbootstrap, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdBootstrap

---

This class initializes the necessary dependencies for the `FbConnection` object — the [DbSetting](/class/firebird/firebirddbsetting), [DbHelper](/class/firebird/firebirddbhelper) and [StatementBuilder](/class/firebird/firebirdstatementbuilder) — and registers them via their respective mappers.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [FirebirdConfiguration.UseFirebird()](/class/firebird/firebirdconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseFirebird();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseFirebird()` more than once has no additional effect.
