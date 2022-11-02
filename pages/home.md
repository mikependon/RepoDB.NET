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

{: .new }
> In the latest release of the library, we have [refactored](https://github.com/mikependon/RepoDB/issues/941) the overall implementations of the [Trace](/feature/tracing) functionality. In addition, the [GlobalConfiguration](/class/globalconfiguration) has been introduced to simplify the library initialization.

{: .warning }
> As we have introduced some breaking changes in the newer version of the library, therefore, it is highly recommended to test your solution with the latest beta release [v1.13.0-beta1](https://www.nuget.org/packages/RepoDb/1.13.0-beta1) before upgrading.

{: .important }
> You can report an issue by [creating a bug](https://github.com/mikependon/RepoDB/issues/new?assignees=mikependon&labels=bug&template=report-a-bug.md&title=Bug%3A+%3CYour+bug%2Fissue+title%3E) directly on our Github repository.

We are glad and thankful for your interest in learning [RepoDB](https://github.com/mikependon/RepoDb). It is an open-source .NET ORM library that bridges the gaps of micro-ORMs and macro-ORMs (aka Full-ORMs). It helps you simplify the switch-over of when to use the BASIC and ADVANCE operations during the development.

The main goal of this library is to hugely improve your development experiences when working with data access technology in .NET ecosystem. We would like you to have an easy access to the underlying database capability while maintaining the good programming coding standard and practices.

## Well, Why?

If you are to use a full-fledged macro-ORM, you will most likely be abstracted by the framework itself. Though it is richer in features, it might limit you to touch and maximize the power of the underlying database being in-used.

On the other hand, if you are to use a bare-metal micro-ORM, there is a tendency that your implementations will be verbose (text-based as SQL). Though you have a lot control with this, it is also tedious and not practical in most cases.

RepoDB mediates both the micro-ORMs and macro-ORMs. It only requires a connection string to open a connection and the entire [feature sets](/feature) can then be utilized right away. It is the main reason why this library is the easiest-to-use ORM.

## Getting Started

Please click any of the link below to fast-track your learnings about this library.

- [SQL Server](/tutorial/get-started-sqlserver)
- [SQLite](/tutorial/get-started-sqlite)
- [MySQL](/tutorial/get-started-mysql)
- [PostgreSQL](/tutorial/get-started-postgresql)

Or, please visit our [installation](/tutorial/installation) page for more information, otherwise, visit our [docs](/docs) page to simplify your navigation.
