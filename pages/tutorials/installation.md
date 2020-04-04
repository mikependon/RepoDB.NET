---
layout: page
title: "Installation of Nuget Packages (RepoDb)"
permalink: /tutorial/installation
tags: [repodb, tutorial, installation, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Installation

The package can be installed using Package Manager Console.

#### Raw SQLs

If you wish to work only with raw-SQLs.

```csharp
> Install-Package RepoDb
```

#### SQL Server

If you wish to work with [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer).

```csharp
> Install-Package RepoDb.SqlServer
```

Or, if you are working with bulk operations.

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

Then call the bootstrapper once.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

#### SQLite

If you wish to work with [SQLite](https://www.nuget.org/packages/RepoDb.SqLite).

```csharp
> Install-Package RepoDb.SqLite
```

Then call the bootstrapper once.

```csharp
RepoDb.SqLiteBootstrap.Initialize();
```

#### MySQL

If you wish to work with [MySQL](https://www.nuget.org/packages/RepoDb.MySql).

```csharp
> Install-Package RepoDb.MySql
```

Then call the bootstrapper once.

```csharp
RepoDb.MySqlBootstrap.Initialize();
```

#### PostgreSQL

If you wish to work with [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql).

```csharp
> Install-Package RepoDb.PostgreSql
```

Then call the bootstrapper once.

```csharp
RepoDb.PostgreSqlBootstrap.Initialize();
```

> Please visit our [documentation](/docs) page to learn more about this library.