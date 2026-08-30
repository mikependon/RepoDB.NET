---
layout: default
sidebar: classes
title: "VerticaBootstrap"
description: "A class that is being used to initialize the necessary settings for the VerticaConnection object."
permalink: /class/vertica/verticabootstrap
tags: [repodb, verticabootstrap, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaBootstrap

---

This class initializes the necessary dependencies for the `VerticaConnection` object — the [DbSetting](/class/vertica/verticadbsetting), [DbHelper](/class/vertica/verticadbhelper) and [StatementBuilder](/class/vertica/verticastatementbuilder) — and registers them via their respective mappers.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [VerticaConfiguration.UseVertica()](/class/vertica/verticaconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseVertica();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseVertica()` more than once has no additional effect.

{: .important }
> Initialization also forces `CultureInfo.CurrentCulture` to `CultureInfo.InvariantCulture` for the calling thread, and for every subsequently-created thread in the process, working around `Vertica.Data` formatting date-like parameter values using the ambient thread culture instead of the invariant one.
