---
layout: default
title: HOME
description: "RepoDB is a hybrid-ORM library for .NET."
nav_order: 0
permalink: /
---

# Welcome to RepoDB
{: .fs-9 }

A hybrid ORM library for .NET.
{: .fs-6 .fw-300 }

[Get started now](/tutorial/get-started-sqlserver){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 } [View it on GitHub](https://github.com/mikependon/RepoDB){: .btn .fs-5 .mb-4 .mb-md-0 }

---

We are glad and thankful for your interest in learning [RepoDB](https://github.com/mikependon/RepoDb). It is an open-source .NET ORM library that bridges the gaps of micro-ORMs and macro-ORMs (aka Full-ORMs). It helps you simplify the switch-over of when to use the BASIC and ADVANCE operations during the development.

The main goal is to improve and simplify your development experiences when working with data access technology in .NET ecosystem. We would like you to have an easy access to the underlying database capability while maintaining the good programming coding standards and practices.

## Why use RepoDB?

If you are to use a full-fledged macro-ORM, you will most likely be abstracted by the framework itself. Though it is richer in features, it might limit you to touch and maximize the power of the underlying database being in-used.

On the other hand, if you are to use a bare-metal micro-ORM, there is a tendency that your implementations will be verbose (text-based as SQL). Though you have a lot control with this, it is also tedious and not practical in most cases.

RepoDB mediates both the micro-ORMs and macro-ORMs. It only requires a connection string to open a connection and the entire [feature sets](/feature) can then be utilized right away. It is the main reason why this library is the easiest-to-use ORM.

| Attribute  | Description |
|:-------------|:------------------|
| Easy-to-Use | The operations were all implemented as extension methods of your IDbConnection object. For as long your connection is open, any operations can then be called against your database. |
| High Performant | It caches the already-generated compiled expressions for future reusabilities and executions. It understands your schema to create the most optimal compiled expression AOT. |
| Memory Efficient | It extracts and caches your object properties, execution contexts, object mappings and SQL statements. It is reusing them all throughout the process of transformations and executions. |
| Dynamic and Hybrid | It provides some advance features of the full-fledged ORMs. It significantly help the developers to simplify the experience when context-switching during the development. |
| Open-Source Software | It is an open-source software and will always be free. It is authored to further improve the .NET data access experiences and solutions, together with the collective ideas of the community. |
| High Quality | It is a high-quality micro-ORM supported by 10K+ real-life Unit and Integration Tests. It is highly tested and is used by various critical systems that are running in the Production environment. |

## Benchmark

RepoDB shows an impressive performance and memory-efficiency if being compared with other ORMs available in the .NET ecosystem.

(It is the fastest and the most-efficient ORM in .NET space nowadays)

<img src="../../assets/images/site/statistics.svg" />

The benchmark statistics shown above is the result of the community-approved ORM bencher [RawDataAccessBencher](https://github.com/FransBouma/RawDataAccessBencher).

You can visit the [benchmark](https://github.com/mikependon/RepoDB#benchmark) section of our Github page to get more information.

## Let us get started

Please click any of the link below to fast-track your learnings about this library.

- [SQL Server](/tutorial/get-started-sqlserver)
- [SQLite](/tutorial/get-started-sqlite)
- [MySQL](/tutorial/get-started-mysql)
- [PostgreSQL](/tutorial/get-started-postgresql)

Or, please visit our [installation](/tutorial/installation) page for more information, otherwise, visit our [docs](/docs) page to simplify your navigation.
