---
layout: page
title: "Folder Structuring (RepoDb)"
permalink: /extensibility/folderstructuring
tags: [repodb, class, folderstructuring, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Folder Structuring

This page will guide you on the folder structuring when extending the library with different RDBMS data providers.

#### Structures

Let us say you are to support the `Snowflakes`, then below are the recommended structures.

```csharp
> RepoDb.Snowflakes
    - RepoDb.Snowflakes.sln
    + RepoDb.Snowflakes
        - RepoDb.Snowflakes.csproj
    + RepoDb.Snowflakes.Tests
        + RepoDb.Snowflakes.IntegrationTests
            - RepoDb.Snowflakes.IntegrationTests.csproj
        + RepoDb.Snowflakes.UnitTests
            - RepoDb.Snowflakes.UnitTests.csproj
```

#### Namespaces

The namespaces of the classes must be the following.

- All classes that is residing inside the "DbHelpers" folder must have a namespace of "RepoDb.DbHelpers".
- All classes that is residing inside the "DbSettings" folder must have a namespace of "RepoDb.DbSettings".
- All classes that is residing inside the "Resolvers" folder must have a namespace of "RepoDb.Resolvers".
- All classes that is residing inside the "StatementBuilders" folder must have a namespace of "RepoDb.StatementBuilders".

> They should not be prefixed with RepoDb.Snowflakes.FolderName.