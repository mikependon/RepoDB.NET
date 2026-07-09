---
layout: default
sidebar: getstarted
title: "Telemetry"
description: "Learn on how to start capturing and publishing RepoDB operation telemetry using RepoDb.Telemetry.Default."
nav_order: 5
permalink: /tutorial/get-started-telemetry
tags: [repodb, tutorial, get-started, telemetry]
parent: GET STARTED
---

# Get Started for Telemetry

---

RepoDB ships with opt-in, drop-in telemetry via [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default), built on top of [RepoDb.Telemetry.Core](https://www.nuget.org/packages/RepoDb.Telemetry.Core). It wires up a default [ITrace](/interface/itrace) that captures every operation ([Insert](/operation/insert), [Query](/operation/query), [Update](/operation/update), [Delete](/operation/delete), etc.) and publishes it to an insights collector — no custom `ITrace` implementation required. See the [Telemetry](/feature/telemetry) feature page for the full picture.

## Installation

Install the library via NuGet using the Package Manager Console.

```csharp
> Install-Package RepoDb.Telemetry.Default
```

## Enable Telemetry

Call the globalized setup method once at application startup.

```csharp
GlobalConfiguration
    .Setup(new GlobalConfigurationOptions { UseRegisteredGlobalTraces = true })
    .UseDefaultTelemetry(
        host: "https://your-collector-host",
        apiKey: "YOUR_API_KEY",
        applicationName: "MyApp",
        groupName: "Default");
```

That's it — every operation across every connection in the application is now traced automatically.

{: .note }
> `UseRegisteredGlobalTraces = true` is required. It tells the library to run every globally registered tracer (this one included) for every operation, without passing a `trace` argument to each call.

## Configuration

For more control, pass a [DefaultTelemetryOption](/class/defaulttelemetryoption) instead of individual arguments.

```csharp
GlobalConfiguration
    .Setup(new GlobalConfigurationOptions { UseRegisteredGlobalTraces = true })
    .UseDefaultTelemetry(
        new DefaultTelemetryOption("MyApp")
        {
            Host = "https://your-collector-host",
            ApiKey = "YOUR_API_KEY",
            Group = "Default",
            Frequency = TimeSpan.FromSeconds(10)
        },
        errorCallback: ex => logger.LogError(ex, "Telemetry publish failed"),
        logger: serilogLogger);
```

| Property | Description | Default |
|:---------|:-------------|:--------|
| Application | Name of the application producing telemetry. | *(required)* |
| Group | Logical grouping for the dashboard. | `"Default"` |
| Host | Collector endpoint to publish to. | `http://localhost:5000` |
| ApiKey | API key sent via `X-API-Key`. Leave empty if the collector does not require one. | `null` |
| Frequency | How often buffered telemetry is flushed. | `5` seconds |

{: .note }
> Calling `UseDefaultTelemetry()` more than once is safe — it reuses the same [DefaultTelemetryTrace](/class/defaulttelemetrytrace) instance rather than starting a new one.

## What Gets Captured

Every captured operation is represented as a [DefaultTelemetryItem](/class/defaulttelemetryitem) — application, group, session id, operation name, start time, statement, elapsed time, cancellation flag, client machine, source assembly, and version. See [TelemetryItem](/class/telemetryitem) for the full property list.

Items are buffered in memory and flushed on the configured `Frequency`, then JSON-serialized, gzip-compressed, and POSTed to your collector. Publish failures never throw — they are routed to the optional `errorCallback` and `logger`.

## Next Steps

- Read the [Telemetry](/feature/telemetry) feature page for how the pipeline fits together.
- Implement [IPublisherRepository](/interface/ipublisherrepository) to publish somewhere other than the default HTTP collector.
