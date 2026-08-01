---
layout: default
sidebar: classes
title: "OracleDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of an Oracle table."
permalink: /class/oracle/oracledbhelper
tags: [repodb, oracledbhelper, oracle]
parent: "Oracle"
grand_parent: CLASSES
---

# OracleDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for Oracle. It queries `ALL_TAB_COLUMNS` (together with primary-key and identity-column metadata) to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements.

It is automatically registered by [OracleBootstrap](/class/oracle/oraclebootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert an Oracle column data type (e.g. `VARCHAR2`) into its equivalent .NET CLR type. Defaults to [OracleDbTypeNameToClientTypeResolver](/class/oracle/oracledbtypenametoclienttyperesolver). |

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new OracleDbHelper(new MyCustomOracleDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<OracleConnection>(dbHelper, true);
```
