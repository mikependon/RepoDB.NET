---
layout: default
sidebar: releases
title: Telemetry (Default)
description: "This page contains the latest information of the releases of RepoDb.Telemetry.Default library."
nav_order: 10
permalink: /release/telemetry-default
parent: RELEASES
---

# Releases for RepoDb.Telemetry.Default

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Telemetry.Default) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Telemetry.Default).

## RepoDb.Telemetry.Default (v0.0.1)

New
{: .label .label-green }

First release of the RepoDB Insights Telemetry solution — a full observability stack for RepoDB's telemetry feature.

### What's included

- PostgreSQL schema for storing telemetry emitted by RepoDB libraries.
- Telemetry Collector API for ingesting and persisting telemetry.
- Telemetry Query API for querying operational and archived telemetry, powering dashboards.
- File Data Sinker service to archive aged telemetry to Parquet.
- Purger service for retention-based cleanup.
- Grafana visualization pre-provisioned with Main and Group dashboards.

### Highlights

- Docker Compose stack wiring all six components together on a shared network.
- Configurable via environment variables (API key, DB password, Grafana admin password, company branding).
- CI/CD pipeline via GitHub Actions for building and publishing each component's Docker image.
- CSV/JSON export support from the Query API.
- Swagger authorization via `X-API-Key` header.

### Docker images

- `repodb/insights-postgres`
- `repodb/telemetry-default-collector`
- `repodb/telemetry-default-query`
- `repodb/telemetry-default-filedatasinker`
- `repodb/telemetry-default-purger`
- `repodb/insights-visualization`

- Referenced the `RepoDb` package `v1.15.0`.
- Referenced the `RepoDb.Telemetry.Core` package `v0.0.1`.


## RepoDb.Telemetry.Default (v0.0.1-alpha1)

- Initial release of the [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default/0.0.1-alpha1) package.
- Provided the default, out-of-the-box implementation of the `RepoDb.Telemetry.Core` abstractions.
- Referenced the `RepoDb` package `v1.14.2`.
- Referenced the `RepoDb.Telemetry.Core` package `v0.0.1-alpha1`.
