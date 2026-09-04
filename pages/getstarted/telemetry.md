---
layout: default
sidebar: getstarted
title: "Telemetry"
description: "Learn on how to start capturing and publishing RepoDB operation telemetry using RepoDb.Telemetry.Default."
nav_order: "~"
permalink: /tutorial/get-started-telemetry
tags: [repodb, tutorial, get-started, telemetry]
parent: GET STARTED
---

# Get Started for Telemetry

---

RepoDB ships with opt-in, drop-in telemetry via [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default). It wires up a default [ITrace](/interface/itrace) that captures every operation ([Insert](/operation/insert), [Query](/operation/query), [Update](/operation/update), [Delete](/operation/delete), etc.) and publishes it to an insights collector.

## Installation

Install the library via NuGet using the Package Manager Console.

```csharp
> Install-Package RepoDb.Telemetry.Default
```

## Setting up Docker

Download the [docker-compose.yml](https://raw.githubusercontent.com/mikependon/RepoDB/refs/heads/master/src/Telemetry/RepoDb.Telemetry.Default/docker-compose.yml) and [.env](https://raw.githubusercontent.com/mikependon/RepoDB/refs/heads/master/src/Telemetry/RepoDb.Telemetry.Default/.env) files into any directory and run the command below:

```bash
cd c:/folder-location
docker compose up -d
```

Once the stack is up, the collector Collector API runs at [`http://localhost:5000`](http://localhost:5000) by default and the Grafana dashboard is at [`http://localhost:3000`](http://localhost:3000).

{: .note }
> Local defaults (e.g. `RepoDB2026`) are for trying things out — override them via a `.env` file before deploying to production.

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

**That's it, you are now done!** Every operation across every connection in the application is now traced automatically.

{: .important }
> `UseRegisteredGlobalTraces = true` is required — it runs every globally registered tracer (this one included) for every operation without an explicit `trace` argument.

For more control, pass a [DefaultTelemetryOption](/class/defaulttelemetryoption) instead of individual arguments.

