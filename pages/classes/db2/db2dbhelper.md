---
layout: default
sidebar: classes
title: "Db2DbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a Db2 table."
permalink: /class/db2/db2dbhelper
tags: [repodb, db2dbhelper, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2DbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for Db2. It queries `SYSCAT.COLUMNS` (schema-qualified by `CURRENT SCHEMA` when none is specified) to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements, and reads back generated identity values via `IDENTITY_VAL_LOCAL()` against `SYSIBM.SYSDUMMY1`.

It is automatically registered by [Db2Bootstrap](/class/db2/db2bootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a Db2 column data type (e.g. `VARCHAR`) into its equivalent .NET CLR type. Defaults to [Db2DbTypeNameToClientTypeResolver](/class/db2/db2dbtypenametoclienttyperesolver). |

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new Db2DbHelper(new MyCustomDb2DbTypeNameToClientTypeResolver());
DbHelperMapper.Add<DB2Connection>(dbHelper, true);
```
