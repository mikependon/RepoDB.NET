---
layout: default
sidebar: classes
title: "DbTypeNameToColumnNameResolver"
description: "A class used to resolve a DbField into its equivalent Firebird column type declaration."
permalink: /class/firebird/dbtypenametocolumnnameresolver
tags: [repodb, dbtypenametocolumnnameresolver, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# DbTypeNameToColumnNameResolver

---

This [IResolver](/interface/iresolver)`<DbField, string>` implementation converts a [DbField](/class/dbfield) into its equivalent Firebird column type declaration (e.g. `NUMERIC(18,2)`, `VARCHAR(255)`, `CHAR(16) CHARACTER SET OCTETS`), reading the field's `DatabaseType`, `Precision`, `Scale` and `Size` to fill in the declaration's parameters — defaulting to `18` precision, `0` scale, and size `1` when not specified.

It is used internally by [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) to generate the column definitions of the pseudo (staging) table backing `BulkMerge`, `BulkUpdate`, `BulkDelete`, `BulkDeleteByKey`, and `BulkInsert` with [FirebirdBulkImportIdentityBehavior.ReturnIdentity](/enumeration/firebird/firebirdbulkimportidentitybehavior).

{: .note }
> An unrecognized or `blob_text` database type falls back to `BLOB SUB_TYPE TEXT` as a safe catch-all.

## Usability

```csharp
var resolver = new DbTypeNameToColumnNameResolver();
var field = new DbField("Amount", false, false, false, typeof(decimal), 10, 18, 2, "decimal", false);
var columnType = resolver.Resolve(field); // "DECIMAL(18,2)"
```
