---
layout: default
sidebar: classes
title: "EnterpriseDbDbHelper"
description: "A helper class that is being used to retrieve the schema information (columns, primary/identity key) of an EnterpriseDB table."
permalink: /class/enterprisedb/enterprisedbdbhelper
tags: [repodb, enterprisedbdbhelper, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbDbHelper

---

This class implements [IDbHelper](/interface/idbhelper) for EnterpriseDB. It queries `information_schema.columns` (joined against `pg_index`/`pg_namespace` to detect primary keys and temporary-schema tables) to build the list of [DbField](/class/dbfield) objects RepoDB uses internally to generate SQL statements.

It is automatically registered by [EnterpriseDbBootstrap](/class/enterprisedb/enterprisedbbootstrap) — you do not need to instantiate it directly under normal use.

## Properties

| Name | Description |
|:-----|:------------|
| DbTypeResolver | The `IResolver<string, Type>` used to convert an EnterpriseDB column type name into its equivalent .NET CLR type. Defaults to [EnterpriseDbDbTypeNameToClientTypeResolver](/class/enterprisedb/enterprisedbdbtypenametoclienttyperesolver). |

## GetScopeIdentity

`GetScopeIdentity`/`GetScopeIdentityAsync` execute `SELECT lastval();` against the connection.

## Retry on OperationInProgress

If the connection is already mid-operation, `GetFields` and `GetScopeIdentity` transparently retry once against a brand-new connection created from the same connection string. The failure is recognized by type name (`NpgsqlOperationInProgressException`) — `RepoDb.Connector.EnterpriseDb` wraps `NpgsqlConnection` directly rather than translating its exceptions, so Npgsql's own exception type surfaces as-is.

## Array Parameter Handling

For an array-valued parameter, the driver infers its `EDBType` directly from the CLR array value — [ClientTypeToEDBDbTypeResolver](/class/enterprisedb/clienttypetoedbdbtyperesolver) is not consulted, since it has no combinable "Array" member to build one from. This helper only steps in for element conversions the driver can't do on its own: `DateOnly[]`/`TimeOnly[]` arrays are converted to `DateTime[]`/`TimeSpan[]` before binding.

## Usability

Only override this if you need a custom type resolver.

```csharp
var dbHelper = new EnterpriseDbDbHelper(new MyCustomEnterpriseDbDbTypeNameToClientTypeResolver());
DbHelperMapper.Add<EDBConnection>(dbHelper, true);
```
