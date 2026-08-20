---
layout: default
sidebar: classes
title: "MariaDbDbTypeToStringNameResolver"
description: "A class used to resolve a MariaDbType into its equivalent MariaDB database string name."
permalink: /class/mariadb/mariadbconnector/mariadbdbtypetostringnameresolver
tags: [repodb, mariadbdbtypetostringnameresolver, mariadbconnector]
parent: "MariaDbConnector"
grand_parent: "MariaDB"
nav_order: 7
---

# MariaDbDbTypeToStringNameResolver

---

This [IResolver](/interface/iresolver)`<MariaDbType, string>` implementation converts a `MariaDbType` enum value into its equivalent MariaDB SQL type name (e.g. `MariaDbType.VarChar` → `VARCHAR`, `MariaDbType.BigInt` → `BIGINT`, `MariaDbType.Enum`/`MariaDbType.Set` → `TEXT`, any of the spatial types (`Geometry`, `Point`, `LineString`, `Polygon`, `MultiPoint`, `MultiLineString`, `MultiPolygon`, `GeometryCollection`) → `GEOMETRY`).

{: .note }
> This resolver is not currently wired into [MariaDbStatementBuilder](/class/mariadb/mariadbconnector/mariadbstatementbuilder) — it is a standalone utility you can use directly if you need to map a `MariaDbType` to its SQL name.

## Usability

```csharp
var resolver = new MariaDbDbTypeToStringNameResolver();
var typeName = resolver.Resolve(MariaDbType.BigInt); // "BIGINT"
```
