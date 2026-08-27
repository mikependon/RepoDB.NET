---
layout: default
sidebar: classes
title: "FirebirdDbTypeNameToClientTypeResolver"
description: "A class used to resolve a Firebird database type name into its equivalent .NET CLR type."
permalink: /class/firebird/firebirddbtypenametoclienttyperesolver
tags: [repodb, firebirddbtypenametoclienttyperesolver, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a Firebird column type name into its equivalent .NET CLR type (e.g. `varchar`/`char`/`blob_text` → `string`, `numeric`/`decimal`/`dec16`/`dec34` → `decimal`, `blob_binary`/`binary`/`varbinary` → `byte[]`). It is the default `DbTypeResolver` used by [FirebirdDbHelper](/class/firebird/firebirddbhelper).

{: .note }
> The names resolved here are the canonical, lowercased type-name strings produced by [FirebirdDbHelper](/class/firebird/firebirddbhelper) out of Firebird's `RDB$FIELD_TYPE`/`RDB$FIELD_SUB_TYPE`/`RDB$CHARACTER_SET_ID` codes — they are not Firebird SQL keywords themselves, and this resolver is not meaningful to call with an arbitrary SQL type name.

## Usability

```csharp
var resolver = new FirebirdDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("varchar"); // typeof(string)
```
