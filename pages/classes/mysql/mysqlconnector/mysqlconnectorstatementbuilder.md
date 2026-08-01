---
layout: default
sidebar: classes
title: "MySqlConnectorStatementBuilder"
description: "A class used to build the SQL statements for MySqlConnector."
permalink: /class/mysql/mysqlconnector/mysqlconnectorstatementbuilder
tags: [repodb, mysqlconnectorstatementbuilder, mysql, mysqlconnector]
parent: "MySQL"
grand_parent: CLASSES
nav_order: 12
---

# MySqlConnectorStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for [MySqlConnector](https://www.nuget.org/packages/MySqlConnector). It is automatically registered by [MySqlConnectorBootstrap](/class/mysql/mysqlconnector/mysqlconnectorbootstrap) — you do not need to instantiate it directly under normal use.

Its SQL generation is functionally identical to [MySqlStatementBuilder](/class/mysql/mysqldata/mysqlstatementbuilder) (MySql.Data) — the same `` `table` ``-quoted, `@`-parameterized SQL, `LIMIT`/`OFFSET` paging, `LAST_INSERT_ID()`-based identity retrieval, multi-row `INSERT ... VALUES ROW(...), ROW(...), ...` for [InsertAll](/operation/insertall), and `INSERT ... ON DUPLICATE KEY UPDATE ...` for [Merge](/operation/merge)/[MergeAll](/operation/mergeall) — only the underlying ADO.NET driver and its `MySqlConnection`/`MySqlDbType` types differ.

{: .note }
> No `convertFieldResolver` is supplied to the base constructor, so a [Field](/class/field) with an explicit `.Type` is not rendered as a `CAST`/`CONVERT` expression for MySQL.

## Constructors

```csharp
public MySqlConnectorStatementBuilder()
public MySqlConnectorStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

The parameterless constructor resolves `dbSetting` via `DbSettingMapper.Get<MySqlConnection>()`, so it can only be used after [MySqlConnectorBootstrap](/class/mysql/mysqlconnector/mysqlconnectorbootstrap) has run.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(MySqlConnection), new MyCustomMySqlConnectorStatementBuilder(dbSetting), true);
```
