---
layout: default
sidebar: classes
title: "PostgreSqlConvertFieldResolver"
description: "A class used to resolve the Field name conversion for PostgreSql."
permalink: /class/postgresql/postgresqlconvertfieldresolver
tags: [repodb, postgresqlconvertfieldresolver, postgresql]
parent: "PostgreSQL"
grand_parent: CLASSES
---

# PostgreSqlConvertFieldResolver

---

This class resolves a [Field](/class/field) into a `CAST(column AS TYPE)` SQL fragment whenever the field carries an explicit `.Type`, using [DbTypeToPostgreSqlStringNameResolver](/class/postgresql/dbtypetopostgresqlstringnameresolver) to determine the PostgreSQL type name. It is used internally by [PostgreSqlStatementBuilder](/class/postgresql/postgresqlstatementbuilder) and is not typically used directly.

## Usability

```csharp
var resolver = new PostgreSqlConvertFieldResolver();
var expression = resolver.Resolve(new Field("Age", typeof(int)), dbSetting);
// CAST("Age" AS INTEGER)
```
