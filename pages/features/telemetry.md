---
layout: default
sidebar: features
title: "Telemetry"
description: "A feature that captures per-operation execution telemetry (SQL statement, elapsed time, session, client, source assembly) via an ITrace-based pipeline and publishes it to an insights collector."
permalink: /feature/telemetry
tags: [repodb, telemetry]
parent: FEATURES
---

# Telemetry

---

This feature captures execution telemetry for every database operation ([Insert](/operation/insert), [Query](/operation/query), [Update](/operation/update), [Delete](/operation/delete), etc.) and publishes it to an insights collector for monitoring and diagnostics.

It is built on top of the [Tracing](/feature/tracing) feature — the [TelemetryTrace](/class/telemetrytrace) class implements [ITrace](/interface/itrace) to hook into the `BeforeExecution()`/`AfterExecution()` pipeline, capture a [TelemetryItem](/class/telemetryitem) per operation, and flush the buffer to a collector on an interval.

Two packages compose this feature:

| Package | Purpose |
|:--------|:--------|
| [RepoDb.Telemetry.Core](https://www.nuget.org/packages/RepoDb.Telemetry.Core) | Defines the contracts and reusable pieces ([TelemetryOption](/class/telemetryoption), [TelemetryItem](/class/telemetryitem), [TelemetryTrace](/class/telemetrytrace), [IPublisherRepository](/interface/ipublisherrepository)). Use this directly only to customize how telemetry is captured or published. |
| [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default) | A ready-to-use implementation. Wires up a default tracer and publishes to an HTTP collector — no custom `ITrace` implementation required. |

## How It Works

- Each operation's execution is captured as a [TelemetryItem](/class/telemetryitem) (application, group, operation name, statement, elapsed time, client machine, source assembly, etc.) via `BeforeExecution()`/`AfterExecution()`.
- Items are buffered in memory and flushed on an interval (`Frequency`, default 5 seconds).
- On flush, the batch is JSON-serialized, gzip-compressed, and POSTed to the configured collector host via [IPublisherRepository](/interface/ipublisherrepository).
- Publish failures never throw — they're routed to an optional `errorCallback` and `logger`.

## Enabling Telemetry

For most applications, [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default) is the fastest path — see the [Get Started](/tutorial/get-started-telemetry) page.

```csharp
GlobalConfiguration
    .Setup(new GlobalConfigurationOptions { UseRegisteredGlobalTraces = true })
    .UseDefaultTelemetry(
        host: "https://your-collector-host",
        apiKey: "YOUR_API_KEY",
        applicationName: "MyApp",
        groupName: "Default");
```

{: .note }
> `UseRegisteredGlobalTraces = true` is required. It tells the library to run every globally registered tracer (this one included) for every operation, without passing a `trace` argument to each call.

## Custom Publishing

To send telemetry somewhere other than the default HTTP collector, implement [IPublisherRepository](/interface/ipublisherrepository) and use it from your own `ITrace`-based class, following the same pattern as [TelemetryTrace](/class/telemetrytrace).

```csharp
public class MyPublisherRepository : IPublisherRepository
{
    public void Publish(TelemetryItem telemetryItem) { /* send to your sink */ }
    public void PublishAsync(TelemetryItem telemetryItem, CancellationToken cancellationToken = default) { /* ... */ }
    public void PublishMany(IEnumerable<TelemetryItem> telemetryItems) { /* ... */ }
    public void PublishManyAsync(IEnumerable<TelemetryItem> telemetryItems, CancellationToken cancellationToken = default) { /* ... */ }
}
```

## Why not OpenTelemetry (OTel)?

This is a deliberate tradeoff, not an oversight. The telemetry pipeline hooks directly into the library's own before/after execution events and serializes a lightweight payload straight to HTTP — skipping OTel's `Span`/`Activity` machinery, resource/attribute mapping, and collector protocol overhead in the hot path. For a library whose value proposition is being a thin, fast layer over ADO.NET, that overhead matters, and the capture shape mirrors the library's own operation model rather than a generalized industry-wide schema.

{: .note }
> An OTel-based collector is planned as a separate, opt-in package for enterprise-grade scenarios (distributed tracing across services, vendor-neutral export to existing observability stacks). Until then, [RepoDb.Telemetry.Default](https://www.nuget.org/packages/RepoDb.Telemetry.Default) is the fast, zero-fuss path to seeing what your operations are doing.

------

## The database image: `repodb/insights-postgres`

This is a stock PostgreSQL 15 image with the schema baked in — no volume mounts or manual migration step required. On first boot, `POSTGRES_DB=repodb_insights` creates the database, and an init script (`01_create_default_tables.sql`, copied into `/docker-entrypoint-initdb.d/`) creates the schema every other service depends on.

**Schema — `public."DefaultTelemetry"`:**

| Column | Type | Notes |
|---|---|---|
| `Group`, `Application`, `SessionId`, `Operation` | `VARCHAR` | Identify the source of the telemetry event. |
| `StartTime` | `TIMESTAMP` | When the operation ran; the primary time-range filter used by every query path. |
| `Statement` | `TEXT` | The executed statement/operation text. |
| `Elapsed` | `FLOAT` | Duration in milliseconds. |
| `IsCancelled` | `BOOLEAN` | Whether the operation was cancelled. |
| `Client`, `Source`, `Version` | `VARCHAR` | Client/environment metadata. |
| `CreatedAt`, `UpdatedAt` | `TIMESTAMP` | Row bookkeeping, default `CURRENT_TIMESTAMP`. |

Five B-tree indexes cover the common filter combinations (`Group`, `Group+Application`, `Group+Application+Operation`, each paired with `StartTime`, plus a bare `StartTime` and `CreatedAt` index), and the table is `CLUSTER`ed on the `Group+Application+Operation+StartTime` index — since almost every read is "give me this group/app/operation over a time window," physically ordering rows that way keeps those scans sequential.

**Connecting from .NET.** Any Postgres client works, but here's a RepoDB-native example that queries the table directly — useful for ad hoc checks against the running container:

```csharp
using System;
using Npgsql;
using RepoDb;

// One-time, at app startup — tells RepoDB how to talk to PostgreSQL
GlobalConfiguration
    .Setup()
    .UsePostgreSql();

var connectionString =
    "Host=localhost;Port=5432;Database=repodb_insights;Username=postgres;Password=RepoDB2026";

using var connection = new NpgsqlConnection(connectionString);

var recent = connection.Query<DefaultTelemetry>(
    where: e => e.Group == "Default" && e.StartTime >= DateTime.UtcNow.AddHours(-1),
    orderBy: OrderField.Parse(new { StartTime = Order.Descending }),
    top: 50);

foreach (var row in recent)
{
    Console.WriteLine($"{row.StartTime:u}  {row.Application,-20} {row.Operation,-10} {row.Elapsed,8:N2} ms");
}

[Map("DefaultTelemetry")]
public class DefaultTelemetry
{
    public string Group { get; set; }
    public string Application { get; set; }
    public string SessionId { get; set; }
    public string Operation { get; set; }
    public DateTime StartTime { get; set; }
    public string Statement { get; set; }
    public double Elapsed { get; set; }
    public bool IsCancelled { get; set; }
    public string Client { get; set; }
    public string Source { get; set; }
    public string Version { get; set; }
    public DateTime CreatedAt { get; set; }
    public DateTime UpdatedAt { get; set; }
}
```

## The Purger service: `repodb/telemetry-default-purger`

A lightweight Python background worker (capped at 0.25 CPU) with a single job: keep the operational table from growing forever. It has no exposed port — it's pure background maintenance.

On startup it runs an immediate purge, then loops indefinitely: compute a cutoff timestamp (`now - RETENTION_PERIOD_IN_MINUTES`, floored to a `FREQUENCY_IN_MINUTES` boundary so runs land on a predictable grid), delete every `DefaultTelemetry` row with `StartTime` older than that cutoff, log how many rows were removed, and sleep until the next cycle. Failures are caught and logged rather than crashing the process, so a transient database hiccup doesn't take the worker down.

Defaults: 7-day retention (`RETENTION_PERIOD_IN_MINUTES=10080`), a 5-minute cycle (`FREQUENCY_IN_MINUTES=5`). Both are configurable via environment variables alongside the required `CONNECTION_STRING`.

## The FileDataSinker service: `repodb/telemetry-default-filedatasinker`

The counterpart to the Purger: instead of deleting old rows, it archives them. This worker (also capped at 0.25 CPU) periodically reads `DefaultTelemetry` rows and writes them out to Parquet files on a shared volume, so the Purger's deletions don't mean the data is gone for good — the Query API can still read it back as "historical" data.

Each cycle: load a JSON checkpoint file (or initialize it to the earliest `StartTime` in the table, on first run); compute the window from that checkpoint up to now; split the window into daily (UTC) chunks so a multi-day gap is archived one day at a time rather than in one giant query; for each chunk, fetch matching rows and merge them into that day's Parquet file if any exist. The checkpoint only advances past chunks that produced rows — an empty day is simply re-checked on the next cycle instead of being marked done, so no data is silently skipped. As with the Purger, failures are caught, logged, and retried on the next cycle.

Defaults: `DIRECTORY_PATH` of `/tmp/repodb/telemetry`, a 60-minute cycle, and up to `20000` history entries retained in the checkpoint file. `CONNECTION_STRING` is required; the source table and timestamp column are fixed in code, matching the Purger's convention.

## The Collector API: `repodb/telemetry-default-collector`

A FastAPI service (port `5000`) with exactly one job: accept telemetry and write it to Postgres. Interactive docs are available at `/docs` (Swagger) and `/redoc`. Every endpoint except the docs routes requires an `X-API-Key` header matching the `API_KEY` environment variable — missing or wrong keys get a `401`.

**Endpoint:**

`POST /v1/telemetry/default/publish` — accepts a base64-encoded, gzip-compressed JSON array of telemetry items in the request body (exactly what `RepoDb.Telemetry.Default` sends on each flush interval). The API decompresses it, decodes the JSON, and bulk-inserts the batch into `DefaultTelemetry`.

**Expected result:** every response is wrapped in a consistent envelope by a response-formatting middleware:

```json
{
  "success": true,
  "elapsed": 0.0123,
  "timestamp": "2026-07-17T09:15:00.123456",
  "data": null
}
```

On failure (bad payload, database error, invalid key), `success` flips to `false` and an `error: { message, type }` object replaces `data`.

## The Query API: `repodb/telemetry-default-query`

A second FastAPI service (port `5001`) that reads telemetry back out — from both the live operational table and the archived Parquet files — and is also what Grafana calls under the hood. Auth is the same shared API key, accepted via either the `X-API-Key` header or an `api_key` query parameter (the latter matters because dashboard links are plain URLs, not header-carrying requests).

**Endpoints:**

- `GET /v1/telemetry/default/operational/query/group/{group}` — queries live data straight from Postgres. Filter with `application` and `operation` query params (`*` matches all), plus an inclusive `start`/`end` ISO 8601 time range.
- `GET /v1/telemetry/default/historical/query/group/{group}` — same filter shape, but reads from the Parquet archive the FileDataSinker writes, for data that's aged out of the operational table.
- `GET /v1/telemetry/default/operational/export/group/{group}` — same query, streamed back as a file download instead of JSON. `format=csv` (default) or `format=json` (gzip-compressed). This is the endpoint the Grafana dashboards' "As CSV..." / "As JSON..." links call directly from the browser.

**Expected result:** the query endpoints return the same success/error envelope as the Collector, with `data` holding a JSON array of matching `DefaultTelemetry` rows ordered by `StartTime` descending. The export endpoint is the one exception — it streams a raw `text/csv` or `application/gzip` payload with a `Content-Disposition: attachment` header, bypassing the envelope entirely since it isn't JSON. CORS is configurable (`ALLOWED_ORIGINS`, `ALLOWED_METHODS`, `ALLOWED_HEADERS`) since this is the one API meant to be called directly from a browser.

## The Visualization image: `repodb/insights-visualization`

An official Grafana image (13.0.2) with the RepoDB Insights Postgres datasource and three dashboards pre-provisioned — nothing to configure by hand after the container starts. Because Grafana's file-based dashboard provisioner doesn't expand `${...}` placeholders inside dashboard JSON (only inside provisioning YAML), a small entrypoint script substitutes `REPODB_COMPANY_NAME`, `REPODB_COMPANY_LOGO`, and `REPODB_API_KEY` into the dashboard files with `sed` at container start, before Grafana ever reads them.

**Dashboards** (all under a **Default** folder):

- **Welcome** — set as the server-wide home dashboard via `GF_DASHBOARDS_DEFAULT_HOME_DASHBOARD_PATH`, so it's the first thing you see after logging in (a user's own starred dashboard or org preference still takes priority).
- **Main** — overall performance stats and a minutely distribution chart across everything, plus a registered-groups table that drills down into...
- **Group** — the same shape scoped to one `$group` / `$application` / `$operation` at a time, with an executions data table underneath.

Both Main and Group default to a 6-hour lookback window with auto-refresh off, and both expose **"As CSV..."** / **"As JSON..."** links on their tables. These call the Query API's `/operational/export` endpoint directly from the browser, with the API key baked into the link at container start — if `REPODB_API_KEY` here doesn't match the Collector/Query API's `API_KEY`, exports fail with an authorization error rather than a broken query.

As for logging: this is an unmodified Grafana image, so there's no custom application logging layer — visibility comes from Grafana's own stdout logs (`docker logs`) and, if you need to dig into raw rows, Grafana's built-in Explore view against the provisioned Postgres datasource. `GF_SECURITY_ADMIN_PASSWORD` sets the admin login (falls back to Grafana's own `admin` default if unset — override this for anything beyond local use), and `GF_USERS_ALLOW_SIGN_UP=false` keeps public self-registration off.
