---
layout: default
sidebar: classes
title: "EnterpriseDbDbTypeNameToClientTypeResolver"
description: "A class used to resolve an EnterpriseDB database type name into its equivalent .NET CLR type."
permalink: /class/enterprisedb/enterprisedbdbtypenametoclienttyperesolver
tags: [repodb, enterprisedbdbtypenametoclienttyperesolver, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts an EnterpriseDB database type name — as returned by `information_schema.columns.data_type` — into its equivalent .NET CLR type (e.g. `character varying`/`text`/`json` → `string`, `numeric`/`money` → `decimal`, `bytea` → `byte[]`, `uuid` → `Guid`). It is the default `DbTypeResolver` used by [EnterpriseDbDbHelper](/class/enterprisedb/enterprisedbdbhelper).

{: .note }
> Geometric types (`box`, `circle`, `line`, `lseg`, `path`, `point`, `polygon`), `pg_lsn`, `tid`, and text-search types (`tsquery`, `tsvector`) have no CLR representation on the Npgsql-backed `RepoDb.Connector.EnterpriseDb` driver this provider is built on. These, along with any other unrecognized type name, fall back to `typeof(object)`.

## Usability

```csharp
var resolver = new EnterpriseDbDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("uuid"); // typeof(Guid)
```
