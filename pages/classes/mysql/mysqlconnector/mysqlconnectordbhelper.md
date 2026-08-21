---
layout: default
sidebar: classes
title: "MySqlConnectorDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a MySqlConnector table."
permalink: /class/mysql/mysqlconnector/mysqlconnectordbhelper
tags: [repodb, mysqlconnectordbhelper, mysql, mysqlconnector]
parent: "MySqlConnector"
grand_parent: CLASSES
nav_order: 4
---

# MySqlConnectorDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for [MySqlConnector](https://www.nuget.org/packages/MySqlConnector). It queries `INFORMATION_SCHEMA.COLUMNS` to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements. Columns with a blob/text-like data type (`blob`, `longtext`, `mediumtext`, `longblob`, `mediumblob`, `tinyblob`, `binary`, `varbinary`) are treated as unbounded — their reported size is ignored.

It also resolves the newly generated identity value via `SELECT LAST_INSERT_ID()`.

It is automatically registered by [MySqlConnectorBootstrap](/class/mysql/mysqlconnector/mysqlconnectorbootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a MySQL column data type (e.g. `varchar`) into its equivalent .NET CLR type. Defaults to [MySqlConnectorDbTypeNameToClientTypeResolver](/class/mysql/mysqlconnector/mysqlconnectordbtypenametoclienttyperesolver). |

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new MySqlConnectorDbHelper(new MyCustomMySqlConnectorDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<MySqlConnection>(dbHelper, true);
```
