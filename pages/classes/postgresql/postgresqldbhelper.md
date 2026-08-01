---
layout: default
sidebar: classes
title: "PostgreSqlDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a PostgreSql table."
permalink: /class/postgresql/postgresqldbhelper
tags: [repodb, postgresqldbhelper, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for PostgreSQL. It queries `information_schema.columns` (joined against `pg_index` for primary-key metadata) to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements.

It also resolves the newly generated identity value via `SELECT lastval()`.

If the underlying `NpgsqlConnection` throws `NpgsqlOperationInProgressException` while a schema or identity query is issued (e.g. a prior command on the same connection is still streaming a result), the class transparently retries the operation on a new connection created from the same connection string.

It is automatically registered by [PostgreSqlBootstrap](/class/postgresql/postgresqlbootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a PostgreSQL column data type (e.g. `character varying`) into its equivalent .NET CLR type. Defaults to [PostgreSqlDbTypeNameToClientTypeResolver](/class/postgresql/postgresqldbtypenametoclienttyperesolver). |

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new PostgreSqlDbHelper(new MyCustomPostgreSqlDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<NpgsqlConnection>(dbHelper, true);
```
