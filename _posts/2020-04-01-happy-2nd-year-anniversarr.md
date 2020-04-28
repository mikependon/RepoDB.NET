---
layout: post
title: "Happy 2nd Year Anniversary"
author: "Michael Camara Pendon"
date: 2020-04-02 12:00:00 +0100
categories: blogs repodb
---

It was 2 years ago when the [first version](https://www.nuget.org/packages/RepoDb/1.0.0) release of [RepoDb](https://github.com/mikependon/RepoDb) was placed at [Nuget.org](https://www.nuget.org/packages/RepoDb).

#### Story

I started writing the first code last March 15, 2018. The first offical class that has been written is [BaseRepository](/class/baserepository). I developed the initial compiler via `IL` and I find it extremely fast. By that time, I am obsessed of beating up Dapper ORM when it comes to performance. I first wrote the following methods ([ExecuteQuery](/operation/executequery), [ExecuteNonQuery](/operation/executenonquery), [ExecuteScalar](/operation/executescalar) and [ExecuteReader](/operation/executescalar). Then, I added some basic methods like ([Insert](/operation/insert), [Delete](/operation/delete), [Update](/operation/update) and [Query](/operation/query)). The implementation is very shallow and NOT-tested.

> The IL code can be found [here](https://github.com/mikependon/RepoDb/blob/master/RepoDb.Core/RepoDb/Reflection/DelegateFactory.cs), but it is not being used by the library now. We are now using the `Linq-Based` compiler (can be found [here](https://github.com/mikependon/RepoDb/blob/master/RepoDb.Core/RepoDb/Reflection/FunctionFactory.cs)).

The next day, I came to office early and check-in the RepoDb code snippets in our internal repository. Few minutes after, I broadcasted an email to all the developers of Orsted A/S at Kuala Lumpur, Malaysia without knowing the effect of it.

I received much criticism from various architects the same day (ie: re-inventing the wheel, too much ambitious, etc).

However, I got a good feedback from my direct Team Lead.

#### Motivations

As a leading developer of the team by that time, we need to deliver a solution that will calculate the power production in a real-time manner. I initially developed the solution by reusing the repository-based implementation of our existing project (using the Dapper ORM). I find it hard to develop a very dynamic repository (like what [BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository)) can do now. I also encountered a lot of problems when using the `Dynamics` and `ExpandoObject`. I giveup when I need to read a data from `View` with a different `ColumnName` as the primary key.

TBH, I am also very good in ADO.NET by mastering it since the start of my career 13 years ago (from today). I also have experience building CRM solutions that has a feature called `Selection-Wizard` in which it allows the user to pre-select the columns they would like to query from the complex relationships of the CRM, and provide reports on top of it. I know that I am capable of creating an ORM and with great motivation of new things to play with.

#### Reddit

I first posted RepoDb in Reddit 2 years ago. The first post is titled **RepoDb - a very fast lightweight ORM and has the perfect spices of Micro-ORM. Faster than Dapper! Rooting for the experts about this.**, and the post can be found [here](https://www.reddit.com/r/csharp/comments/8y5pm3/repodb_a_very_fast_lightweight_orm_and_has_the/).

A lot of people joined the discussion and the thread has top the `r/csharp` in just few minutes. I saw a lot up/down votes and some frustrations from the community. Honestly, I do not know how to deligently answer the questions from the community, could be the reason of the frustrations.

I remember a one comment that brought me to [RawDataAccessBencher](https://github.com/FransBouma/RawDataAccessBencher) ORM bencher of FransBouma. He wants me to test the library there. The result was, RepoDb is the second most slowest ORM and I was totally shocked actually. I know that the IL I developed is very fast, but by that time, it is only very fast for extracting million of rows (in single iteration) (mabye the fastest), but is very slow if you bench it with its execution mean-deviation (includes multiple iterations by batch).

I analyzed the culprits in the code snippets and also tried to optimized the IL. I solved the problem and brought RepoDb as the fastest ORM a month after. The official technique was posted by me on my old blog (can be found [here](https://codesdirectory.blogspot.com/2018/09/repodb-net-lightweight-orm-library.html)).

It was also 2 years ago (4 months after pushing the first version), RepoDb has been officially accepted by FransBouma to be a part of the [RawDataAccessBencher](https://github.com/FransBouma/RawDataAccessBencher) ORM bencher. That is amazing and gave me more spirit to futher develop the library.


