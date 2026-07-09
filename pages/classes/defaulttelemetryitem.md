---
layout: default
sidebar: classes
title: "DefaultTelemetryItem"
description: "The concrete telemetry data model used by RepoDb.Telemetry.Default."
permalink: /class/defaulttelemetryitem
tags: [repodb, defaulttelemetryitem]
parent: CLASSES
---

# DefaultTelemetryItem

---

A concrete, [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default)-specific subclass of [TelemetryItem](/class/telemetryitem). It adds no fields of its own — it exists so the default pipeline has its own type to construct, serialize, and evolve independently of [RepoDb.Telemetry.Core](https://www.nuget.org/packages/RepoDb.Telemetry.Core).

See [TelemetryItem](/class/telemetryitem) for the full list of properties.
