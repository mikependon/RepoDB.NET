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
> We are introducing an insights capability to expose library activity within your application. Stay tuned! Visit our [story](https://github.com/mikependon/RepoDB/issues/1217) to share your thoughts.

{: .highlight }
> Starting v1.14.0, the support to .NET 8, .NET 9 and .NET 10 has been introduced, while we have removed the support to .NET 6 and .NET 7 respectively.

[RepoDB](https://github.com/mikependon/RepoDb) is an open-source .NET ORM library that bridges the gap between micro-ORMs and macro-ORMs (full-ORMs). It simplifies the decision of when to use basic versus advanced data-access operations during development.

The library's primary goal is to improve developer productivity when working with data-access technology in the .NET ecosystem — providing direct access to underlying database capabilities while promoting clean coding standards and practices.

## Why RepoDB?

A full macro-ORM abstracts away the database, which — while feature-rich — can prevent you from fully leveraging the underlying database engine.

A bare-metal micro-ORM gives you fine-grained control, but implementations tend to be verbose and SQL-heavy, making them tedious and impractical at scale.

RepoDB occupies the middle ground. It requires only a connection string to open a connection, after which the complete [feature set](/feature) is immediately available — making it the easiest-to-use ORM for .NET.

## Getting Started

Choose a database to get started quickly:

- [SQL Server](/tutorial/get-started-sqlserver)
- [SQLite](/tutorial/get-started-sqlite)
- [MySQL](/tutorial/get-started-mysql)
- [PostgreSQL](/tutorial/get-started-postgresql)

For setup instructions, visit the [installation](/tutorial/installation) page. For a full topic index, visit the [docs](/docs) page.

{: .important }
> Report issues by [creating a bug](https://github.com/mikependon/RepoDB/issues/new?assignees=mikependon&labels=bug&template=report-a-bug.md&title=Bug%3A+%3CYour+bug%2Fissue+title%3E) directly in our GitHub repository.
