---
layout: default
sidebar: classes
title: "Db2DbTypeNameToClientTypeResolver"
description: "A class used to resolve a Db2 database type name into its equivalent .NET CLR type."
permalink: /class/db2/db2dbtypenametoclienttyperesolver
tags: [repodb, db2dbtypenametoclienttyperesolver, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2DbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a Db2 column data type name — as returned by `SYSCAT.COLUMNS.TYPENAME` — into its equivalent .NET CLR type (e.g. `VARCHAR` → `string`, `DECIMAL`/`NUMERIC` → `decimal`, `BLOB` → `byte[]`). It also handles the `TIMESTAMP(n) [WITH TIME ZONE]` qualifier form. It is the default `DbTypeResolver` used by [Db2DbHelper](/class/db2/db2dbhelper).

## Usability

```csharp
var resolver = new Db2DbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("VARCHAR"); // typeof(string)
```
