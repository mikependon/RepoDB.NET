---
layout: default
title: "Telemetry"
nav_order: 8
has_children: true
permalink: /class/telemetry
parent: CLASSES
---

# Classes (Telemetry)

---

Classes that implement RepoDB's built-in telemetry capability, split between the abstractions used to capture and publish operation data ([TelemetryItem](/class/telemetryitem), [TelemetryOption](/class/telemetryoption), [TelemetryTrace](/class/telemetrytrace), [TelemetryPublisherRepository](/class/telemetrypublisherrepository)) and the concrete `RepoDb.Telemetry.Default` implementations wired up by `UseDefaultTelemetry()` ([DefaultTelemetryItem](/class/defaulttelemetryitem), [DefaultTelemetryOption](/class/defaulttelemetryoption), [DefaultTelemetryTrace](/class/defaulttelemetrytrace), [DefaultTelemetryPublisherRepository](/class/defaulttelemetrypublisherrepository)).
