---
layout: default
sidebar: classes
title: "MariaDbDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a MySql.Data-based MariaDb table."
permalink: /class/mariadb/mariadb/mariadbdbhelper
tags: [repodb, mariadbdbhelper, mariadb]
parent: "MariaDb"
grand_parent: CLASSES
nav_order: 4
---

# MariaDbDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for the `MySql.Data`-based [RepoDb.MariaDb](https://www.nuget.org/packages/RepoDb.MariaDb) package. It queries `INFORMATION_SCHEMA.COLUMNS` to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements. Columns with a blob/text-like data type (`blob`, `blobasarray`, `binary`, `longtext`, `mediumtext`, `longblob`, `mediumblob`, `tinyblob`, `varbinary`) are treated as unbounded — their reported size is ignored.

It also resolves the newly generated identity value via `SELECT LAST_INSERT_ID()`.

It is automatically registered by [MariaDbBootstrap](/class/mariadb/mariadb/mariadbbootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a MariaDB column data type (e.g. `varchar`) into its equivalent .NET CLR type. Defaults to [MariaDbDbTypeNameToClientTypeResolver](/class/mariadb/mariadb/mariadbdbtypenametoclienttyperesolver). |

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new MariaDbDbHelper(new MyCustomMariaDbDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<MariaDbConnection>(dbHelper, true);
```
