---
layout: page
title: "Installation (SQL Server)"
permalink: /tutorials/installation
tags: [repodb, tutorial, installation, orm, hybrid-orm, sqlserver]
---

## Installation

The package can be installed using Package Manager Console.

#### Raw SQLs

```csharp
> Install-Package RepoDb
```

#### SQL Server

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

```csharp
> Install-Package RepoDb.SqLite
```

Then call the bootstrapper once.

```csharp
RepoDb.SqLiteBootstrap.Initialize();
```

#### MySQL

```csharp
> Install-Package RepoDb.MySql
```

Then call the bootstrapper once.

```csharp
RepoDb.MySqlBootstrap.Initialize();
```

#### PostgreSQL

```csharp
> Install-Package RepoDb.PostgreSql
```

Then call the bootstrapper once.

```csharp
RepoDb.PostgreSqlBootstrap.Initialize();
```