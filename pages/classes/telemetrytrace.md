---
layout: default
sidebar: classes
title: "TelemetryTrace"
description: "An ITrace implementation that captures the library's operation telemetry and publishes it to the insights solution."
permalink: /class/telemetrytrace
tags: [repodb, telemetrytrace]
parent: CLASSES
---

# TelemetryTrace

---

An [ITrace](/interface/itrace) implementation that hooks into the `BeforeExecution()`/`AfterExecution()` pipeline, buffers a [TelemetryItem](/class/telemetryitem) per operation, and flushes the buffer to an [IPublisherRepository](/interface/ipublisherrepository) on a timer. See the [Telemetry](/feature/telemetry) feature page for the full picture.

## Creating an Instance

```csharp
var option = new TelemetryOption("MyApp")
{
    Host = "https://your-collector-host",
    ApiKey = "YOUR_API_KEY"
};

var trace = new TelemetryTrace(option, errorCallback: ex => logger.LogError(ex, "Telemetry error"));
trace.Start();
```

## Methods

| Name | Description |
|:-----|:------------|
| Start | Starts the internal flush timer, using the `Frequency` from the passed [TelemetryOption](/class/telemetryoption). |
| BeforeExecution | Captures the start of an operation. Implements [ITrace](/interface/itrace). |
| AfterExecution | Captures the result/elapsed time of an operation. Implements [ITrace](/interface/itrace). |

## Attaching to Operations

Pass it explicitly to an operation call.

```csharp
connection.QueryAll<Customer>(trace: trace);
```

Or register it so it applies to every operation across the application.

```csharp
GlobalConfiguration
    .Setup(new GlobalConfigurationOptions { UseRegisteredGlobalTraces = true });

GlobalTraceRegistration.Register(trace);
```

{: .note }
> [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default) uses [DefaultTelemetryTrace](/class/defaulttelemetrytrace), a singleton subclass of this class, wired up automatically via `UseDefaultTelemetry()`.
