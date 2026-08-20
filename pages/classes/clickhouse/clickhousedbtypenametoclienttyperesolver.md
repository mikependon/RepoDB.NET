---
layout: default
sidebar: classes
title: "ClickHouseDbTypeNameToClientTypeResolver"
description: "A class used to resolve a ClickHouse database type name into its equivalent .NET CLR type."
permalink: /class/clickhouse/clickhousedbtypenametoclienttyperesolver
tags: [repodb, clickhousedbtypenametoclienttyperesolver, clickhouse]
parent: "ClickHouse"
grand_parent: CLASSES
---

# ClickHouseDbTypeNameToClientTypeResolver

---

This [IResolver](/interface/iresolver)`<string, Type>` implementation converts a ClickHouse column data type name — as returned by `system.columns.type` — into its equivalent .NET CLR type. It first unwraps any `Nullable(...)`/`LowCardinality(...)` wrapper and parameterized suffix (e.g. `FixedString(16)`, `DateTime64(3)`), then maps the base type name, for example: `Int8`/`Int16`/`Int32`/`Int64` → `sbyte`/`short`/`int`/`long`, `UInt8`/`UInt16`/`UInt32`/`UInt64` → `byte`/`ushort`/`uint`/`ulong`, `Float32`/`Float64` → `float`/`double`, `String`/`FixedString` → `string`, `Date`/`DateTime`/`DateTime64` → `DateTime`, `Decimal`/`Decimal32/64/128/256` → `decimal`, `UUID` → `Guid`, and `Bool` → `bool`. It is the default `DbTypeResolver` used by [ClickHouseDbHelper](/class/clickhouse/clickhousedbhelper).

## Usability

```csharp
var resolver = new ClickHouseDbTypeNameToClientTypeResolver();
var clrType = resolver.Resolve("Nullable(UInt64)"); // typeof(ulong)
```
