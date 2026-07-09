---
layout: default
sidebar: classes
title: "DefaultTelemetryTrace"
description: "The singleton ITrace implementation created and registered by UseDefaultTelemetry()."
permalink: /class/defaulttelemetrytrace
tags: [repodb, defaulttelemetrytrace]
parent: CLASSES
---

# DefaultTelemetryTrace

---

A singleton subclass of [TelemetryTrace](/class/telemetrytrace) used by [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default). It is created and started internally by [DefaultTelemetryGlobalConfiguration.UseDefaultTelemetry()](/class/defaulttelemetryglobalconfiguration) — there is no need to instantiate it directly.

## Properties

| Name | Description |
|:-----|:------------|
| Instance | Gets the singleton instance. Throws `InvalidOperationException` if accessed before `UseDefaultTelemetry()` has been called. |

{: .note }
> Calling `UseDefaultTelemetry()` more than once is safe — it reuses the same singleton instance rather than starting a new one.
