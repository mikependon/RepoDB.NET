---
layout: default
sidebar: classes
title: "DefaultTelemetryOption"
description: "The settings class used to configure RepoDb.Telemetry.Default."
permalink: /class/defaulttelemetryoption
tags: [repodb, defaulttelemetryoption]
parent: CLASSES
---

# DefaultTelemetryOption

---

A subclass of [TelemetryOption](/class/telemetryoption) used to configure [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default). It inherits all properties from [TelemetryOption](/class/telemetryoption) without adding any of its own.

## Creating an Instance

```csharp
var option = new DefaultTelemetryOption("MyApp")
{
    Host = "https://your-collector-host",
    ApiKey = "YOUR_API_KEY",
    Group = "Default",
    Frequency = TimeSpan.FromSeconds(10)
};
```

## Passing to Setup

```csharp
GlobalConfiguration
    .Setup(new GlobalConfigurationOptions { UseRegisteredGlobalTraces = true })
    .UseDefaultTelemetry(option,
        errorCallback: ex => logger.LogError(ex, "Telemetry publish failed"),
        logger: serilogLogger);
```
