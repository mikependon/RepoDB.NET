---
layout: default
sidebar: getstarted
title: "Installation"
description: "Learn on how to install RepoDB library on your Project/Solution."
permalink: /tutorial/installation
tags: [repodb, tutorial, installation]
parent: GET STARTED
nav_exclude: true
search_exclude: true
---

# Installation

Install packages using the Package Manager Console.

## Raw SQLs

For raw SQL usage only:

```csharp
> Install-Package RepoDb
```

> It supports all kinds of RDBMS data providers.

## SQL Server

To work with [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer):

```csharp
> Install-Package RepoDb.SqlServer
```

After installation, call the globalized setup method to initialize all dependencies for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer).

```csharp
GlobalConfiguration
	.Setup()
	.UseSqlServer();
```

For versions prior to 1.13.0, use the bootstrapper below.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

For bulk operations:

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

#### System.Data.SqlClient

When using this package, bootstrap the connection object at startup.

```csharp
var dbSetting = new SqlServerDbSetting();

DbSettingMapper
	.Add<System.Data.SqlClient.SqlConnection>(dbSetting, true);
DbHelperMapper
	.Add<System.Data.SqlClient.SqlConnection>(new SqlServerDbHelper(), true);
StatementBuilderMapper
	.Add<System.Data.SqlClient.SqlConnection>(new SqlServerStatementBuilder(dbSetting), true);
```

Alternatively, replicate the [SqlServerBootstrap](https://github.com/mikependon/RepoDB/blob/master/RepoDb.SqlServer/RepoDb.SqlServer/SqlServerBootstrap.cs) class and call the initializer explicitly.

## PostgreSQL

To work with [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql):

```csharp
> Install-Package RepoDb.PostgreSql
```

After installation, call the globalized setup method to initialize all dependencies for PostgreSql.

```csharp
GlobalConfiguration
	.Setup()
	.UsePostgreSql();
```

For versions prior to 1.13.0, use the bootstrapper below.

```csharp
RepoDb.PostgreSqlBootstrap.Initialize();
```

For bulk operations:

```csharp
> Install-Package RepoDb.PostgreSql.BulkOperations
```

## MySQL

Two packages are available for MySQL.

#### MySql.Data

To work with [RepoDb.MySql](https://www.nuget.org/packages/RepoDb.MySql):

```csharp
> Install-Package RepoDb.MySql
```

After installation, call the globalized setup method to initialize all dependencies for MySQL.

```csharp
GlobalConfiguration
	.Setup()
	.UseMySql();
```

For versions prior to 1.13.0, use the bootstrapper below.

```csharp
RepoDb.MySqlBootstrap.Initialize();
```

#### MySqlConnector

To work with [RepoDb.MySqlConnector](https://www.nuget.org/packages/RepoDb.MySqlConnector):

```csharp
> Install-Package RepoDb.MySqlConnector;
```

After installation, call the globalized setup method to initialize all dependencies for MySQL.

```csharp
GlobalConfiguration
	.Setup()
	.UseMySqlConector();
```

For versions prior to 1.13.0, use the bootstrapper below.

```csharp
RepoDb.MySqlConnectorBootstrap.Initialize();
```

## SQLite

To work with [RepoDb.Sqlite.Microsoft](https://www.nuget.org/packages/RepoDb.Sqlite.Microsoft):

```csharp
> Install-Package RepoDb.Sqlite.Microsoft
```

After installation, call the globalized setup method to initialize all dependencies for SQLite.

```csharp
GlobalConfiguration
	.Setup()
	.UseSqlite();
```

For versions prior to 1.13.0, use the bootstrapper below.

```csharp
RepoDb.SqliteBootstrap.Initialize();
```

{: .note }
> Please visit our [documentation](/docs) page to learn more about this library.
