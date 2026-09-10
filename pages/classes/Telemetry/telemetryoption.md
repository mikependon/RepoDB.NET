---
layout: default
sidebar: classes
title: "TelemetryOption"
description: "A class that defines the necessary settings to capture and publish the library's telemetry."
permalink: /class/telemetryoption
tags: [repodb, telemetryoption]
parent: "Telemetry"
grand_parent: CLASSES
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
| CertificateValidationCallback | An optional callback used to validate the server certificate presented by the collector API when publishing over HTTPS. Leave `null` to use the default .NET certificate validation. Useful when the collector API is deployed with a self-signed or otherwise untrusted certificate. |

## Creating an Instance

```csharp
var option = new TelemetryOption("MyApp")
{
    Host = "https://your-collector-host",
    ApiKey = "YOUR_API_KEY",
    Group = "Default",
    Frequency = TimeSpan.FromSeconds(5),
    CertificateValidationCallback = (request, certificate, chain, errors) => true
};
```

{: .warning }
> Only bypass certificate validation like the example above for a trusted, self-signed collector (e.g. local development). For production, validate the presented certificate/chain yourself instead of unconditionally returning `true`.

{: .note }
> [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default) uses [DefaultTelemetryOption](/class/defaulttelemetryoption), a subclass of this class.
