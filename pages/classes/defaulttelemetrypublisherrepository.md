---
layout: default
sidebar: classes
title: "DefaultTelemetryPublisherRepository"
description: "The IPublisherRepository implementation used by RepoDb.Telemetry.Default."
permalink: /class/defaulttelemetrypublisherrepository
tags: [repodb, defaulttelemetrypublisherrepository]
parent: CLASSES
---

# DefaultTelemetryPublisherRepository

---

A subclass of [TelemetryPublisherRepository](/class/telemetrypublisherrepository) used internally by [DefaultTelemetryTrace](/class/defaulttelemetrytrace). It inherits all behavior from [TelemetryPublisherRepository](/class/telemetrypublisherrepository) without adding any of its own — publishing is still a gzip-compressed HTTP POST to `{host}/v1/telemetry/publish`.
