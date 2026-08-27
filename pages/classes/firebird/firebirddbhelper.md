---
layout: default
sidebar: classes
title: "FirebirdDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a Firebird table."
permalink: /class/firebird/firebirddbhelper
tags: [repodb, firebirddbhelper, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for Firebird. It queries `RDB$RELATION_FIELDS`/`RDB$FIELDS` (joined against `RDB$RELATION_CONSTRAINTS`/`RDB$INDEX_SEGMENTS` for primary-key detection) to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements, mapping each column's `RDB$FIELD_TYPE`/`RDB$FIELD_SUB_TYPE`/`RDB$CHARACTER_SET_ID` triple into a canonical type-name string consumed by [FirebirdDbTypeNameToClientTypeResolver](/class/firebird/firebirddbtypenametoclienttyperesolver).

It is automatically registered by [FirebirdBootstrap](/class/firebird/firebirdbootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a Firebird column type name into its equivalent .NET CLR type. Defaults to [FirebirdDbTypeNameToClientTypeResolver](/class/firebird/firebirddbtypenametoclienttyperesolver). |

## GetScopeIdentity

`GetScopeIdentity`/`GetScopeIdentityAsync` always throw `NotSupportedException`.

{: .important }
> Firebird has no session-wide "last identity" construct equivalent to SQL Server's `SCOPE_IDENTITY()` or MySQL's `LAST_INSERT_ID()` — identity/generator values are scoped per-generator, not per-session. The generated key is already returned directly by [Insert](/operation/insert)/[Merge](/operation/merge) via Firebird's `RETURNING` clause; query the underlying generator explicitly (e.g. `GEN_ID(generator_name, 0)`, found in `RDB$RELATION_FIELDS.RDB$GENERATOR_NAME`) if you need it out-of-band.

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new FirebirdDbHelper(new MyCustomFirebirdDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<FbConnection>(dbHelper, true);
```
