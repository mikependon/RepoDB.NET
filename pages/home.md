---
layout: default
title: HOME
description: "RepoDB is a production-ready data access platform for .NET applications."
nav_order: 0
permalink: /
---

# Welcome to RepoDB
{: .fs-9 }

A hybrid ORM library for .NET.
{: .fs-6 .fw-300 }

[Get started now](/tutorial/get-started-sqlserver){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 } [View it on GitHub](https://github.com/mikependon/RepoDB){: .btn .fs-5 .mb-4 .mb-md-0 }

---

[RepoDB](https://github.com/mikependon/RepoDb) is a high-performance, open-source data access platform for .NET applications. It combines the simplicity of a micro-ORM with the productivity of a full ORM, giving developers the freedom to use raw SQL for precise control or fluent operations for faster development—all through the same IDbConnection.

Write raw SQL when you need full control. Use fluent methods when you want productivity. Switch between them freely, in the same codebase.

The library's primary goal is to improve developer productivity when working with data-access technology in the .NET ecosystem — providing direct access to underlying database capabilities while promoting clean coding standards and practices.

## Why RepoDB?

A full macro-ORM abstracts away the database, which — while feature-rich — can prevent you from fully leveraging the underlying database engine.

A bare-metal micro-ORM gives you fine-grained control, but implementations tend to be verbose and SQL-heavy, making them tedious and impractical at scale.

RepoDB occupies the middle ground. It requires only a connection string to open a connection, after which the complete [feature set](/feature) is immediately available — making it the easiest-to-use ORM for .NET.

## Default Telemetry

{: .new }
> We are excited to announce the availability of the [Telemetry](https://blogs.repodb.net/announcements/2026/07/repodb-default-telemetry) feature of RepoDB — a free, powerful capability that lets you observe exactly what your application is doing under the hood.

**We will set it up for you in a minute!** 
Visit our detailed tutorial from blogging site - [Getting Started with RepoDB Insights Default Telemetry](https://blogs.repodb.net/posts/2026/07/getting-started-with-repodb-insights-default-telemetry).

<img src="https://raw.githubusercontent.com/mikependon/RepoDB.Resources/refs/heads/main/images/repodb-insights-default-telemetry-banner.png" />

It's part of the ecosystem we're building around RepoDB for observing and monitoring the data pipelines that depend on the library. You no longer have to guess how your data layer is behaving — Telemetry gives developers direct visibility into what's happening behind the scenes.

We're shipping this capability because we believe observability is a key differentiator for a modern ORM, and a vital piece of infrastructure for data pipelines and any application that depends on reliable data access.

In this initial release, we're publishing the [Default Telemetry](/tutorial/get-started-telemetry) capability, which captures key metrics from your application and sends them to a self-hosted insights solution that runs entirely within your own environment.

<img src="https://raw.githubusercontent.com/mikependon/RepoDB.Resources/refs/heads/main/blogs/images/announcements/2026-07-17/grafana-welcome-dashboard.png" style="max-wdith:1024px" />

<img src="https://raw.githubusercontent.com/mikependon/RepoDB.Resources/refs/heads/main/blogs/images/announcements/2026-07-17/grafana-main-dashboard-p1.png" style="max-wdith:1024px" />

Build with confidence, without the overhead of wiring up your own instrumentation — and without worrying about where your data ends up. It's free to use.

RepoDB's [Telemetry](/feature/telemetry) feature captures per-operation execution data (statement, elapsed time, session, client) via [`RepoDb.Telemetry.Default`](https://www.nuget.org/packages/RepoDb.Telemetry.Default) and publishes it to this solution's Collector API for storage and visualization.

## Getting Started

Choose a database to get started quickly:

- [SQL Server](/tutorial/get-started-sqlserver)
- [SQLite](/tutorial/get-started-sqlite)
- [MySQL](/tutorial/get-started-mysql)
- [PostgreSQL](/tutorial/get-started-postgresql)

For setup instructions, visit the [installation](/tutorial/installation) page. For a full topic index, visit the [docs](/docs) page.

{: .important }
> Report issues by [creating a bug](https://github.com/mikependon/RepoDB/issues/new?assignees=mikependon&labels=bug&template=report-a-bug.md&title=Bug%3A+%3CYour+bug%2Fissue+title%3E) directly in our GitHub repository.
