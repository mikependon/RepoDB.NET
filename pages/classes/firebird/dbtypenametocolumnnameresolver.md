---
layout: default
sidebar: classes
title: "DbTypeNameToColumnNameResolver"
description: "A class used to resolve a Firebird database type name into its equivalent base Firebird column type keyword."
permalink: /class/firebird/dbtypenametocolumnnameresolver
tags: [repodb, dbtypenametocolumnnameresolver, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# DbTypeNameToColumnNameResolver

---

This [IResolver](/interface/iresolver)`<string, string>` implementation converts a Firebird database type name — e.g. a [DbField](/class/dbfield)'s `DatabaseType` — into its equivalent *base* Firebird column type keyword (e.g. `numeric` → `NUMERIC`, `varchar` → `VARCHAR`, `binary`/`varbinary` → `CHAR`/`VARCHAR`). Sized types (`numeric`, `decimal`, `char`, `varchar`, `binary`, `varbinary`) are returned without their `(precision,scale)`/`(size)` portion — the caller is expected to append that using the field's own precision/scale/size.

It is used internally by [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) to generate the column definitions of the pseudo (staging) table backing `BulkMerge`, `BulkUpdate`, `BulkDelete`, `BulkDeleteByKey`, and `BulkInsert` with [FirebirdBulkImportIdentityBehavior.ReturnIdentity](/enumeration/firebird/firebirdbulkimportidentitybehavior). There, the base keyword this resolver returns is combined with the field's `Precision`/`Scale`/`Size` (defaulting to `18`/`0`/`1` respectively when unset) and, for `binary`/`varbinary`, a trailing `CHARACTER SET OCTETS` — none of which this resolver appends itself.

{: .note }
> An unrecognized or `blob_text` database type falls back to `BLOB SUB_TYPE TEXT` as a safe catch-all.

## Usability

```csharp
var resolver = new DbTypeNameToColumnNameResolver();
var baseType = resolver.Resolve("decimal"); // "DECIMAL"

// The caller appends sizing/charset itself, e.g.:
var columnType = $"{baseType}({field.Precision ?? 18},{field.Scale ?? 0})"; // "DECIMAL(18,2)"
```
