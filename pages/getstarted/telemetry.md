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

RepoDB ships with opt-in, drop-in telemetry via [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default), built on top of [RepoDb.Telemetry.Core](https://www.nuget.org/packages/RepoDb.Telemetry.Core). It wires up a default [ITrace](/interface/itrace) that captures every operation ([Insert](/operation/insert), [Query](/operation/query), [Update](/operation/update), [Delete](/operation/delete), etc.) and publishes it to an insights collector.

## Installation

Install the library via NuGet using the Package Manager Console.

```csharp
> Install-Package RepoDb.Telemetry.Default
```

## Setting up Docker

Download the [docker-compose.yml](https://raw.githubusercontent.com/mikependon/RepoDB/refs/heads/master/RepoDb.Telemetry.Default/docker-compose.yml) and [.env](https://raw.githubusercontent.com/mikependon/RepoDB/refs/heads/master/RepoDb.Telemetry.Default/.env) files into any directory and run the command below:

```bash
cd c:/folder-location
docker compose up -d
```

{: .note }
> Everything runs with local defaults (`RepoDB2026` as the shared password/API key) meant for trying things out, not production. Override them with environment variables in a `.env` file before deploying to production.

Once the stack is up, the collector Collector API runs at [`http://localhost:5000`](http://localhost:5000) by default and the Grafana dashboard is at [`http://localhost:3000`](http://localhost:3000).

## Enable Telemetry

Call the globalized setup method once at application startup.

```csharp
GlobalConfiguration
    .Setup(new GlobalConfigurationOptions { UseRegisteredGlobalTraces = true })
    .UseDefaultTelemetry(
        host: "https://your-collector-host",
        apiKey: "YOUR_API_KEY",
        applicationName: "<YOUR_APPLICATION_NAME>",
        groupName: "<YOUR_APPLICATION_GROUP>");
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
        new DefaultTelemetryOption("<YOUR_APPLICATION_NAME>")
        {
            Host = "https://your-collector-host",
            ApiKey = "YOUR_API_KEY",
            Group = "<YOUR_APPLICATION_GROUP>",
            Frequency = TimeSpan.FromSeconds(1)
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
