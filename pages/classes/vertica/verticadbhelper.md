---
layout: default
sidebar: classes
title: "VerticaDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a Vertica table."
permalink: /class/vertica/verticadbhelper
tags: [repodb, verticadbhelper, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for Vertica. It queries Vertica's own `v_catalog.columns`/`v_catalog.primary_keys` system tables to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements, stripping the `(size)`/`(precision,scale)` suffix off each column's raw `data_type` value down to its base type-name keyword before handing it to [VerticaDbTypeNameToClientTypeResolver](/class/vertica/verticadbtypenametoclienttyperesolver).

It is automatically registered by [VerticaBootstrap](/class/vertica/verticabootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a Vertica column type name into its equivalent .NET CLR type. Defaults to [VerticaDbTypeNameToClientTypeResolver](/class/vertica/verticadbtypenametoclienttyperesolver). |

## GetScopeIdentity

`GetScopeIdentity`/`GetScopeIdentityAsync` execute `SELECT LAST_INSERT_ID()` against the connection.

{: .important }
> This has not been verified against a live Vertica instance. Verify that `LAST_INSERT_ID()` is genuinely supported by your Vertica version/session before relying on it in production.

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new VerticaDbHelper(new MyCustomVerticaDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<VerticaConnection>(dbHelper, true);
```
