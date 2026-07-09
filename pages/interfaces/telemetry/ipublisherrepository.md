---
layout: default
sidebar: interfaces
title: "IPublisherRepository (Telemetry)"
permalink: /interface/telemetry/ipublisherrepository
tags: [repodb, ipublisherrepository]
parent: INTERFACES
---

# IPublisherRepository

---

This interface is used to mark a class to be a telemetry publisher. It gives you full control over how [TelemetryItem](/class/telemetryitem) objects are delivered to your insights solution (e.g. HTTP collector, message queue, file sink, etc).

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Publish | Publishes a single [TelemetryItem](/class/telemetryitem). |
| PublishMany | Publishes multiple [TelemetryItem](/class/telemetryitem) objects in one batch. |

{: .note }
> Both methods have their corresponding `Async` methods.

## Use-Cases

Use this interface if the default HTTP collector used by [TelemetryPublisherRepository](/class/telemetrypublisherrepository) does not fit your infrastructure — for example, publishing to a message queue, a different observability backend, or a custom on-prem endpoint.

## How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class MyPublisherRepository : IPublisherRepository
{
    public void Publish(TelemetryItem telemetryItem)
    {
        // Send to your sink
    }

    public void PublishAsync(TelemetryItem telemetryItem, CancellationToken cancellationToken = default)
    {
        // Send to your sink asynchronously
    }

    public void PublishMany(IEnumerable<TelemetryItem> telemetryItems)
    {
        // Send a batch to your sink
    }

    public void PublishManyAsync(IEnumerable<TelemetryItem> telemetryItems, CancellationToken cancellationToken = default)
    {
        // Send a batch to your sink asynchronously
    }
}
```

## Usability

Use it from your own [ITrace](/interface/itrace)-based class, following the same pattern as [TelemetryTrace](/class/telemetrytrace).

{: .note }
> Please visit the [Telemetry](/feature/telemetry) feature page for more information on how publishing fits into the overall pipeline.
