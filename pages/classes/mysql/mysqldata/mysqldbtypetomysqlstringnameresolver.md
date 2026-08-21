---
layout: default
sidebar: classes
title: "MySqlDbTypeToMySqlStringNameResolver"
description: "A class used to resolve a MySqlDbType (MySql.Data) into its equivalent MySQL database string name."
permalink: /class/mysql/mysqldata/mysqldbtypetomysqlstringnameresolver
tags: [repodb, mysqldbtypetomysqlstringnameresolver, mysql]
parent: "MySql"
grand_parent: CLASSES
nav_order: 7
---

# MySqlDbTypeToMySqlStringNameResolver

---

This [IResolver](/interface/iresolver)`<MySqlDbType, string>` implementation converts a [MySql.Data](https://www.nuget.org/packages/MySql.Data) `MySqlDbType` enum value into its equivalent MySQL SQL type name (e.g. `MySqlDbType.VarChar` → `VARCHAR`, `MySqlDbType.Int64` → `BIGINT`, `MySqlDbType.Guid` → `TEXT`).

{: .note }
> Unlike its SQL Server/Oracle/PostgreSQL counterparts, this resolver is not currently wired into [MySqlStatementBuilder](/class/mysql/mysqldata/mysqlstatementbuilder) — it is a standalone utility you can use directly if you need to map a `MySqlDbType` to its SQL name.

## Usability

```csharp
var resolver = new MySqlDbTypeToMySqlStringNameResolver();
var typeName = resolver.Resolve(MySqlDbType.Int64); // "BIGINT"
```
