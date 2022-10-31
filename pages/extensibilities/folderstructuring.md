---
layout: default
sidebar: extensibilities
title: "Folder Structuring"
permalink: /extensibility/folderstructuring
tags: [repodb, folderstructuring]
parent: EXTENSIBILITIES
---

# Folder Structuring

---

This page will guide you on the folder structuring when extending the library with different RDBMS data providers.

## Structures

Let us say you are to support a new data provider, then below are the recommended structures.

```csharp
+ RepoDb.<NewDataProvider>
    - RepoDb.<NewDataProvider>.sln
    + RepoDb.<NewDataProvider>
        - RepoDb.<NewDataProvider>.csproj
    + RepoDb.<NewDataProvider>.Tests
        + RepoDb.<NewDataProvider>.IntegrationTests
            - RepoDb.<NewDataProvider>.IntegrationTests.csproj
        + RepoDb.<NewDataProvider>.UnitTests
            - RepoDb.<NewDataProvider>.UnitTests.csproj
```

## Namespaces

The namespaces of the classes must be the following.

- All classes that is residing inside the `DbHelpers` folder must have a namespace of `RepoDb.DbHelpers`.
- All classes that is residing inside the `DbSettings` folder must have a namespace of `RepoDb.DbSettings`.
- All classes that is residing inside the `Resolvers` folder must have a namespace of `RepoDb.Resolvers`.
- All classes that is residing inside the `StatementBuilders` folder must have a namespace of `RepoDb.StatementBuilders`.

{: .important }
> They should not be prefixed with RepoDb.<NewDataProvider>.FolderName.