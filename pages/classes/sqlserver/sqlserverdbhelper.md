---
layout: default
sidebar: classes
title: "SqlServerDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a SQL Server table."
permalink: /class/sqlserver/sqlserverdbhelper
tags: [repodb, sqlserverdbhelper, sqlserver]
parent: "SQL Server"
grand_parent: CLASSES
---

# SqlServerDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for SQL Server. It queries `INFORMATION_SCHEMA.COLUMNS` (joined against `sys.columns` and the primary-key constraint metadata) to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements.

It also resolves the newly generated identity value via `SELECT COALESCE(SCOPE_IDENTITY(), @@IDENTITY)`.

It is automatically registered by [SqlServerBootstrap](/class/sqlserver/sqlserverbootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a SQL Server column data type (e.g. `varchar`) into its equivalent .NET CLR type. Defaults to [SqlServerDbTypeNameToClientTypeResolver](/class/sqlserver/sqlserverdbtypenametoclienttyperesolver). |

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new SqlServerDbHelper(new MyCustomSqlServerDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<SqlConnection>(dbHelper, true);
```
