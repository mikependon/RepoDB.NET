---
layout: default
sidebar: classes
title: "SqlServerConvertFieldResolver"
description: "A class used to resolve the Field name conversion for SQL Server."
permalink: /class/sqlserver/sqlserverconvertfieldresolver
tags: [repodb, sqlserverconvertfieldresolver, sqlserver]
parent: "SQL Server"
grand_parent: CLASSES
---

# SqlServerConvertFieldResolver

---

This class resolves a [Field](/class/field) into a `CONVERT(TYPE, column)` SQL fragment whenever the field carries an explicit `.Type`, using [DbTypeToSqlServerStringNameResolver](/class/sqlserver/dbtypetosqlserverstringnameresolver) to determine the SQL Server type name. It is used internally by [SqlServerStatementBuilder](/class/sqlserver/sqlserverstatementbuilder) and is not typically used directly.

## Usability

```csharp
var resolver = new SqlServerConvertFieldResolver();
var expression = resolver.Resolve(new Field("Age", typeof(int)), dbSetting);
// CONVERT([INT], [Age])
```
