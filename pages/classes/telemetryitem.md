---
layout: default
sidebar: classes
title: "TelemetryItem"
description: "A class that defines the telemetry data captured for a single operation, to be published to the insights solution."
permalink: /class/telemetryitem
tags: [repodb, telemetryitem]
parent: CLASSES
---

# TelemetryItem

---

Represents a single captured [Telemetry](/feature/telemetry) record for one operation execution (e.g. [Insert](/operation/insert), [Query](/operation/query)). Instances are created internally by [TelemetryTrace](/class/telemetrytrace) and passed to [IPublisherRepository](/interface/ipublisherrepository) for publishing.

## Properties

| Name | Description |
|:-----|:------------|
| Application | Name of the application that produced the telemetry. |
| Group | The logical group the application is categorized under. Defaults to `"Default"`. |
| SessionId | Unique identifier correlating an operation's before/after execution logs. |
| Operation | The operation name (e.g. `Insert`, `Query`, `Update`, `Delete`). |
| StartTime | UTC timestamp of when the operation started executing. |
| Statement | The SQL statement (or equivalent) that was executed. |
| Elapsed | Total elapsed time of the operation, in milliseconds. |
| IsCancelled | Whether the operation was cancelled from within a trace callback. |
| Client | The machine name the operation ran on. |
| Source | The name of the assembly that hosts/consumes the library (your application). |
| Version | The version of that assembly. |

{: .note }
> [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default) uses [DefaultTelemetryItem](/class/defaulttelemetryitem), a concrete subclass of this class.
