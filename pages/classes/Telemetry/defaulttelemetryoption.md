---
layout: default
sidebar: classes
title: "DefaultTelemetryOption"
description: "The settings class used to configure RepoDb.Telemetry.Default."
permalink: /class/defaulttelemetryoption
tags: [repodb, defaulttelemetryoption]
parent: "Telemetry"
grand_parent: CLASSES
---

# DefaultTelemetryOption

---

A subclass of [TelemetryOption](/class/telemetryoption) used to configure [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default). It inherits all properties from [TelemetryOption](/class/telemetryoption) without adding any of its own.

## Properties

| Property | Description | Default |
|:---------|:-------------|:--------|
| Application | Name of the application producing telemetry. | *(required)* |
| Group | Logical grouping for the dashboard. | `"Default"` |
| Host | Collector endpoint to publish to. | `http://localhost:5000` |
| ApiKey | API key sent via `X-API-Key`. Leave empty if the collector does not require one. | `null` |
| Frequency | How often buffered telemetry is flushed. | `5` seconds |
| CertificateValidationCallback | Callback used to validate the server certificate presented by the collector API when publishing over HTTPS. | `null` (default .NET certificate validation) |

## Creating an Instance

```csharp
var option = new DefaultTelemetryOption("<YOUR_APPLICATION_NAME>")
{
    Host = "https://your-collector-host",
    ApiKey = "YOUR_API_KEY",
    Group = "<YOUR_APPLICATION_GROUP>",
    Frequency = TimeSpan.FromSeconds(1),
    CertificateValidationCallback = (request, certificate, chain, errors) => true
};
```

{: .warning }
> Only bypass certificate validation like the example above for a trusted, self-signed collector (e.g. local development). For production, validate the presented certificate/chain yourself instead of unconditionally returning `true`.

## Passing to Setup

```csharp
GlobalConfiguration
    .Setup(new GlobalConfigurationOptions { UseRegisteredGlobalTraces = true })
    .UseDefaultTelemetry(option,
        errorCallback: ex => logger.LogError(ex, "Telemetry publish failed"),
        logger: serilogLogger);
```
