---
layout: default
sidebar: classes
title: "SapHanaDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of a SAP HANA table."
permalink: /class/saphana/saphanadbhelper
tags: [repodb, saphanadbhelper, saphana]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for SAP HANA. It queries SAP HANA's own `SYS.TABLE_COLUMNS`/`SYS.CONSTRAINTS` system views, scoped to the connection's `CURRENT_SCHEMA`, to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements.

It is automatically registered by [SapHanaBootstrap](/class/saphana/saphanabootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert a SAP HANA column type name into its equivalent .NET CLR type. Defaults to [SapHanaDbTypeNameToClientTypeResolver](/class/saphana/saphanadbtypenametoclienttyperesolver). |

## GetScopeIdentity

`GetScopeIdentity`/`GetScopeIdentityAsync` execute `SELECT CURRENT_IDENTITY_VALUE() FROM DUMMY;` against the connection.

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new SapHanaDbHelper(new MyCustomSapHanaDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<HanaConnection>(dbHelper, true);
```
