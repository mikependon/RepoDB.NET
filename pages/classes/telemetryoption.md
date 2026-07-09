---
layout: default
sidebar: classes
title: "TelemetryOption"
description: "A class that defines the necessary settings to capture and publish the library's telemetry."
permalink: /class/telemetryoption
tags: [repodb, telemetryoption]
parent: CLASSES
---

# TelemetryOption

---

Defines the settings used by [TelemetryTrace](/class/telemetrytrace) to capture and publish [Telemetry](/feature/telemetry) data.

## Properties

| Name | Description |
|:-----|:------------|
| Application | The name of the application that produces the telemetry. Set via the constructor. |
| Group | The group the application is categorized under. Defaults to `"Default"`. |
| Host | The collector endpoint to publish to. Defaults to `http://localhost:5000`. |
| ApiKey | The API key sent via the `X-API-Key` header. Leave `null` if the collector does not require one. |
| Frequency | How often the buffered telemetry is flushed. Defaults to `5` seconds. |

## Creating an Instance

```csharp
var option = new TelemetryOption("MyApp")
{
    Host = "https://your-collector-host",
    ApiKey = "YOUR_API_KEY",
    Group = "Default",
    Frequency = TimeSpan.FromSeconds(5)
};
```

{: .note }
> [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default) uses [DefaultTelemetryOption](/class/defaulttelemetryoption), a subclass of this class.
