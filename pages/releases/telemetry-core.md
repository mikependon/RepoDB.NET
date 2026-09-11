---
layout: default
sidebar: releases
title: Telemetry (Core)
description: "This page contains the latest information of the releases of RepoDb.Telemetry.Core library."
nav_order: "~1"
permalink: /release/telemetry-core
parent: RELEASES
---

# Releases for RepoDb.Telemetry.Core

---

View the NuGet package [here](https://www.nuget.org/packages/RepoDb.Telemetry.Core) or download it directly [here](https://www.nuget.org/api/v2/package/RepoDb.Telemetry.Core).

## RepoDb.Telemetry.Core (v1.16.1)

Released: September 11, 2026

New
{: .label .label-green }

- Added the `CertificateValidationCallback` property to [TelemetryOption](/class/telemetryoption), letting [TelemetryPublisherRepository](/class/telemetrypublisherrepository) validate (or override validation of) the server certificate presented by the collector API when publishing over HTTPS. Leave it `null` to use the default .NET certificate validation — useful when the collector is deployed with a self-signed or otherwise untrusted certificate. [#1327](https://github.com/mikependon/RepoDB/issues/1327)
- Referenced the `RepoDb` package `v1.16.0`.


## RepoDb.Telemetry.Core (v1.16.0)

Released: September 9, 2026

New
{: .label .label-green }

- Version bumped to `v1.16.0` to stay aligned with the rest of the RepoDB package family as part of the major release. No functional changes since `v0.0.1` — see that entry below for the full feature set.
- Referenced the `RepoDb` package `v1.16.0`.


## RepoDb.Telemetry.Core (v0.0.1)

New
{: .label .label-green }

- First stable release of the [RepoDb.Telemetry.Core](https://www.nuget.org/packages/RepoDb.Telemetry.Core/0.0.1) package, moving out of alpha.
- Released alongside the first release of the RepoDB Insights Telemetry solution — a full observability stack for RepoDB's telemetry feature. See the [RepoDb.Telemetry.Default](/release/telemetry-default) release notes for the full stack details.
- Referenced the `RepoDb` package `v1.15.0`.


## RepoDb.Telemetry.Core (v0.0.1-alpha1)

- Initial release of the [RepoDb.Telemetry.Core](https://www.nuget.org/packages/RepoDb.Telemetry.Core/0.0.1-alpha1) package.
- Introduced instrumentation for RepoDB operations, enabling diagnostics and tracing of query execution.
- Referenced the `RepoDb` package `v1.14.2`.
