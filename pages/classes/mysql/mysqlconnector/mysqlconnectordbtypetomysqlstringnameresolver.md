---
layout: default
sidebar: classes
title: "MySqlConnectorDbTypeToMySqlStringNameResolver"
description: "A class used to resolve a MySqlDbType (MySqlConnector) into its equivalent MySQL database string name."
permalink: /class/mysql/mysqlconnector/mysqlconnectordbtypetomysqlstringnameresolver
tags: [repodb, mysqlconnectordbtypetomysqlstringnameresolver, mysql, mysqlconnector]
parent: "MySQL"
grand_parent: CLASSES
nav_order: 14
---

# MySqlConnectorDbTypeToMySqlStringNameResolver

---

This [IResolver](/interface/iresolver)`<MySqlDbType, string>` implementation converts a [MySqlConnector](https://www.nuget.org/packages/MySqlConnector) `MySqlDbType` enum value into its equivalent MySQL SQL type name (e.g. `MySqlDbType.VarChar` → `VARCHAR`, `MySqlDbType.Int64` → `BIGINT`, `MySqlDbType.Guid` → `TEXT`).

{: .note }
> `MySqlConnector.MySqlDbType` is a distinct enum from `MySql.Data.MySqlClient.MySqlDbType` used by [MySqlDbTypeToMySqlStringNameResolver](/class/mysql/mysqldata/mysqldbtypetomysqlstringnameresolver) — the two are not interchangeable, even though this resolver's logic mirrors it member-for-member. Like its MySql.Data counterpart, it is a standalone utility and is not currently wired into [MySqlConnectorStatementBuilder](/class/mysql/mysqlconnector/mysqlconnectorstatementbuilder).

## Usability

```csharp
var resolver = new MySqlConnectorDbTypeToMySqlStringNameResolver();
var typeName = resolver.Resolve(MySqlDbType.Int64); // "BIGINT"
```
