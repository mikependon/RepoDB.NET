---
layout: post
title: "RepoDb remains as the most-fastest and most-efficient ORM in .NET Core Ecosystem (v3.1)"
author: "Michael Camara Pendon"
date: 2020-04-10 20:47:00 +0100
categories: blogs repodb
---

Yesterday (April 9, 2020), I had issued a PR to FransBouma's [RawDataAccessBencher](https://github.com/FransBouma/RawDataAccessBencher) with the latest version of [RepoDb](https://github.com/mikependon/RepoDb), plus with the following updates:

- Upgrading the `System.Data.SqlClient` to version `4.8.0`.
- Referencing the `Microsoft.Data.SqlClient` version `1.1.1`.
- Added the `Initialize()` method in the [BencherBase](https://github.com/FransBouma/RawDataAccessBencher/blob/master/RawBencher/Benchers/BencherBase.cs) class.

By today (April 10, 2020), FransBouma reach out to me to have some collaborations about the PR (can be found [here](https://github.com/FransBouma/RawDataAccessBencher/pull/50)), in which was approved in few hours after.

Right after the PR's approval, I received a Tweet about the bencher's execution.

<img src="/assets/images/blogs/rdab/2020-04-10/Tweet.PNG" height="300" />

**RepoDb remains as the fastest and the most-efficient in .NET ORM library in SET fetches. It is 2 years in a row as of writing this.**

> Please exclude the `HandCoded Materializer` result when the outcome of the bencher.

#### Result for NET Core (Set Fetches)

<img src="/assets/images/blogs/rdab/2020-04-10/NetCore.PNG" height="420" />

The actual result can be found [here](https://github.com/FransBouma/RawDataAccessBencher/blob/master/Results/20200410_netcore31.txt).

#### Result for NET Framework (Set Fetches)

<img src="/assets/images/blogs/rdab/2020-04-10/NetFX.PNG" height="420" />

The actual result can be found [here](https://github.com/FransBouma/RawDataAccessBencher/blob/master/Results/20200410_netfx.txt).

#### Result for NET Core (Individual Fetches)

RepoDb has downed to 4th place (previous 1st place) in INDIVIDUAL fetches. See the result below.

<img src="/assets/images/blogs/rdab/2020-04-10/NetCoreIndividual.PNG" height="420" />

The actual result can be found [here](https://github.com/FransBouma/RawDataAccessBencher/blob/ac9310c0992dd3a9ed4ffdd01a4bee18bef2fb40/Results/20200410_netcore31.txt#L113).

The reason to this is the update at `v1.10.x`. There is a feature that has been introduced to RepoDb that needs to be executed in every calls to [ExecuteQuery](/operation/executequery) method. This would affect both the INDIVIDUAL and SET fetches. However, the library is fast enough when converting big chunk of records, the effect to SET fetches is not that much.
