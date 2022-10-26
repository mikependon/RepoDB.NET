---
layout: default
sidebar: getstarted
title: "Installation"
description: "Learn on how to install RepoDB library on your Project/Solution."
permalink: /tutorial/installation
tags: [repodb, tutorial, installation]
parent: GET STARTED
---

# Installation

The packages can be installed using the Package Manager Console window.

### Raw SQLs

If you wish to work only with raw-SQLs.

```csharp
> Install-Package RepoDb
```

> It supports all kinds of RDBMS data providers.

### SQL Server

If you wish to work with [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer).

```csharp
> Install-Package RepoDb.SqlServer
```

Or, if you are to work with the bulk operations.

```csharp
> Install-Package RepoDb.SqlServer.BulkOperations
```

Then, call the bootstrapper to initialize the needed references.

```csharp
GlobalConfiguration
	.Setup()
	.UseSqlServer();
```

For the users prior the version 1.13.0, use the code below.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

#### System.Data.SqlClient

If you are working with this package, you are required to bootstrap the connection object on the startup.

```csharp
var dbSetting = new SqlServerDbSetting();

DbSettingMapper
	.Add<System.Data.SqlClient.SqlConnection>(dbSetting, true);
DbHelperMapper
	.Add<System.Data.SqlClient.SqlConnection>(new SqlServerDbHelper(), true);
StatementBuilderMapper
	.Add<System.Data.SqlClient.SqlConnection>(new SqlServerStatementBuilder(dbSetting), true);
```

Or, you can replicate the actual [SqlServerBootstrap](https://github.com/mikependon/RepoDB/blob/master/RepoDb.SqlServer/RepoDb.SqlServer/SqlServerBootstrap.cs) class implementation and attach it to your solution. Then, call the local class initializer method explicitly.

### PostgreSQL

If you wish to work with [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql).

```csharp
> Install-Package RepoDb.PostgreSql
```

Or, if you are to work with the bulk operations.

```csharp
> Install-Package RepoDb.PostgreSql.BulkOperations
```

Then, call the bootstrapper to initialize the needed references.

```csharp
GlobalConfiguration
	.Setup()
	.UsePostgreSql();
```

For the users prior the version 1.13.0, use the code below.

```csharp
RepoDb.PostgreSqlBootstrap.Initialize();
```

### MySQL

There are 2 packages available for MySQL.

#### MySql.Data

If you wish to work with [RepoDb.MySql](https://www.nuget.org/packages/RepoDb.MySql).

```csharp
> Install-Package RepoDb.MySql
```

Then, call the bootstrapper to initialize the needed references.

```csharp
GlobalConfiguration
	.Setup()
	.UseMySql();
```

For the users prior the version 1.13.0, use the code below.

```csharp
RepoDb.MySqlBootstrap.Initialize();
```

#### MySqlConnector

If you wish to work with [RepoDb.MySqlConnector](https://www.nuget.org/packages/RepoDb.MySqlConnector).

```csharp
> Install-Package RepoDb.MySqlConnector;
```

Then, call the bootstrapper to initialize the needed references.

```csharp
GlobalConfiguration
	.Setup()
	.UseMySqlConector();
```

For the users prior the version 1.13.0, use the code below.

```csharp
RepoDb.MySqlConnectorBootstrap.Initialize();
```

### SQLite

There are 2 packages available for SQLite.

#### System.Data.SQLite.Core

If you wish to work with [RepoDb.SQLite.System](https://www.nuget.org/packages/RepoDb.SQLite.System).

```csharp
> Install-Package RepoDb.SQLite.System
```

Then, call the bootstrapper to initialize the needed references.

```csharp
GlobalConfiguration
	.Setup()
	.UseSQLite();
```

For the users prior the version 1.13.0, use the code below.

```csharp
RepoDb.SQLiteBootstrap.Initialize();
```

#### Microsoft.Data.Sqlite

If you wish to work with [RepoDb.Sqlite.Microsoft](https://www.nuget.org/packages/RepoDb.Sqlite.Microsoft).

```csharp
> Install-Package RepoDb.Sqlite.Microsoft
```

Then, call the bootstrapper to initialize the needed references.

```csharp
GlobalConfiguration
	.Setup()
	.UseSqlite();
```

For the users prior the version 1.13.0, use the code below.

```csharp
RepoDb.SqliteBootstrap.Initialize();
```

> Please visit our [documentation](/docs) page to learn more about this library.
