---
layout: default
sidebar: classes
title: "TelemetryPublisherRepository"
description: "The default IPublisherRepository implementation; gzip-compresses and publishes telemetry data over HTTP."
permalink: /class/telemetrypublisherrepository
tags: [repodb, telemetrypublisherrepository]
parent: "Telemetry"
grand_parent: CLASSES
---

# TelemetryPublisherRepository

---

The default [IPublisherRepository](/interface/ipublisherrepository) implementation. JSON-serializes and gzip-compresses telemetry items, then POSTs them to `{host}/v1/telemetry/publish`, adding an `X-API-Key` header when an API key is provided.

## Creating an Instance

```csharp
var repository = new TelemetryPublisherRepository(
    host: "https://your-collector-host",
    apiKey: "YOUR_API_KEY",
    errorCallback: ex => logger.LogError(ex, "Publish failed"),
    logger: serilogLogger,
    certificateValidationCallback: (request, certificate, chain, errors) => true);
```

{: .note }
> `certificateValidationCallback` is optional and validates the server certificate presented by the collector API when publishing over HTTPS. Leave it `null` to use the default .NET certificate validation — only override it for a trusted, self-signed collector.

## Methods

| Name | Description |
|:-----|:------------|
| Publish | Publishes a single [TelemetryItem](/class/telemetryitem). |
| PublishMany | Publishes multiple [TelemetryItem](/class/telemetryitem) objects in one batch. |

{: .note }
> The methods above have corresponding `Async` variants. Publish failures never throw — they are routed to the optional `errorCallback` and `logger` instead.

{: .note }
> [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default) uses [DefaultTelemetryPublisherRepository](/class/defaulttelemetrypublisherrepository), a subclass of this class.
