---
layout: default
title: Home
description: "RepoDB is a hybrid-ORM library for .NET. It is the best alternative ORM to both Dapper and EntityFramework."
nav_order: 0
permalink: /
---

# Welcome to RepoDB
{: .fs-9 }

A hybrid ORM library for .NET.
{: .fs-6 .fw-300 }

[Get started now](/tutorial/get-started-sqlserver){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 } [View it on GitHub](https://github.com/mikependon/RepoDB){: .btn .fs-5 .mb-4 .mb-md-0 }

---

We are glad and thankful for your interest in learning [RepoDB](https://github.com/mikependon/RepoDb). It is an open-source .NET ORM library that bridge the gaps of micro-ORMs and macro-ORMs (aka Full-ORMs). It helps you simplify the switch-over of when to use the BASIC and ADVANCE operations during the development.

Its name is a shortcut to Repository (a popular pattern when implementing Data Access Layers) + the word Database.

Diagram below shows the high-level difference of RepoDB from both the micro-ORM and macro-ORM.

<img src="../../assets/images/site/difference.svg" />

Even the library is rich on features (i.e. [Batch](/feature/batchoperations)/[Bulk](/feature/bulkoperations), [Cache](/feature/caching), [Repositories](/feature/repositories), etc), it is still on the space of micro-ORM.

---

### Why use RepoDB?

The main goal is to improve the things and simplify your development experiences when working with data access technology in .NET ecosystem. We would like you to have an easy access to the underlying data store capability while maintaining the good programming coding standards.

If you are to use a full-fledged macro-ORM, you are most likely be abstracted by the framework itself. Though they are more richer in operations, but those operations are limiting you to touch and maximize the power of the underlying datasource being in-used.

On the other hand, if you are to use a bare-metal micro-ORM, there are tendencies that most of your implementation will be verbose and are all text-based (SQLs). Even though you have a lot control with this, but it is quite tedious and is not practical in most cases.

RepoDB mediates both the micro-ORMs and macro-ORMs. When using this library, your development experience is as simple as Dapper when opening a connection and is as simple as Entity Framework when calling the operations. It is the reason that makes this library the easiest-to-use ORM.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Customer, int>(new Customer
    {
        Name = "John",
        Doe = "Doe")
    });
}
```

There is a lot more from [here](/feature). You can also use the library to work with [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer), [SQLite](https://www.nuget.org/packages/RepoDb.SqLite), [MySQL](https://www.nuget.org/packages/RepoDb.MySql) and [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql) RDBMS.

<details>
<summary>Important Attributes</summary>

<p>

    <p>
        <b>Easy to Use</b> - the operations were all implemented as extension methods of your IDbConnection object. For as long your connection is open, any operations can then be called against your database.
    </p>

    <p>
        <b>High Performant</b> - it caches the already-generated compiled expressions for future reusabilities and executions. It understands your schema to create the most optimal compiled expression AOT.
    </p>

    <p>
        <b>Memory Efficient</b> - it extracts and caches your object properties, execution contexts, object mappings and SQL statements. It is reusing them all throughout the process of transformations and executions.
    </p>

    <p>
        <b>Dynamic and Hybrid</b> - it provides some advance features of the full-fledged ORMs. It significantly help the developers to simplify the experience when context-switching during the development.
    </p>

    <p>
        <b>Open-Source Software</b> - it is an open-source software and will always be free. It is authored to further improve the .NET data access experiences and solutions, together with the collective ideas of the community.
    </p>

    <p>
        <b>High Quality</b> - it is a high-quality micro-ORM supported by 10K+ real-life Unit and Integration Tests. It is highly tested and is used by various critical systems that are running in the Production environment.
    </p>

</p>

</details>

---

### Official Benchmark

RepoDB shows an impressive performance and memory-efficiency if being compared with other ORMs available in the .NET ecosystem.

It is the fastest and the most-efficient ORM in .NET space nowadays!

<img src="../../assets/images/site/statistics.svg" />

The benchmark statistics shown above is the result of the community-approved ORM bencher [RawDataAccessBencher](https://github.com/FransBouma/RawDataAccessBencher).

Please visit the [benchmark](https://github.com/mikependon/RepoDB#benchmark) section of our Github page to get more information.

---

### Let us get started

Please click any of the link below to fast-track your learnings about this library.

- [SQL Server](/tutorial/get-started-sqlserver)
- [SQLite](/tutorial/get-started-sqlite)
- [MySQL](/tutorial/get-started-mysql)
- [PostgreSQL](/tutorial/get-started-postgresql)

Or, please visit our [installation](/tutorial/installation) page for more information, otherwise, visit our [docs](/docs) page to simplify your navigation.
