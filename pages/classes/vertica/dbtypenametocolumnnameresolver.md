---
layout: default
sidebar: classes
title: "DbTypeNameToColumnNameResolver"
description: "A class used to resolve a Vertica database type name into its equivalent base Vertica column type keyword."
permalink: /class/vertica/dbtypenametocolumnnameresolver
tags: [repodb, dbtypenametocolumnnameresolver, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# DbTypeNameToColumnNameResolver

---

This [IResolver](/interface/iresolver)`<string, string>` implementation converts a Vertica database type name — e.g. a [DbField](/class/dbfield)'s `DatabaseType` — into its equivalent *base* Vertica column type keyword (e.g. `numeric` → `NUMERIC`, `varchar` → `VARCHAR`). Sized types (`numeric`, `decimal`, `char`, `varchar`, `binary`, `varbinary`) are returned without their `(precision,scale)`/`(size)` portion — the caller is expected to append that using the field's own precision/scale/size. An unrecognized database type falls back to `LONG VARCHAR`, Vertica's large-text type.

It is used internally by [RepoDb.Vertica.BulkOperations](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations) to generate the column definitions of the pseudo (staging) table backing `BulkMerge`, `BulkUpdate`, `BulkDelete`, `BulkDeleteByKey`, and `BulkInsert` with [VerticaBulkImportIdentityBehavior.ReturnIdentity](/enumeration/vertica/verticabulkimportidentitybehavior).

{: .note }
> This class shares its unqualified name with Firebird's own [DbTypeNameToColumnNameResolver](/class/firebird/dbtypenametocolumnnameresolver) — the two live in separate assemblies/namespaced provider folders, so there is no compile-time conflict, but the same name resolves differently depending on which provider package is referenced.

## Usability

```csharp
var resolver = new DbTypeNameToColumnNameResolver();
var baseType = resolver.Resolve("decimal"); // "DECIMAL"

// The caller appends sizing itself, e.g.:
var columnType = $"{baseType}({field.Precision ?? 18},{field.Scale ?? 0})"; // "DECIMAL(18,2)"
```
