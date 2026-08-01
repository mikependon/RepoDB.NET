---
layout: default
sidebar: classes
title: "OracleStatementBuilder"
description: "A class used to build the SQL statements for Oracle Database 12c and later."
permalink: /class/oracle/oraclestatementbuilder
tags: [repodb, oraclestatementbuilder, oracle]
parent: "Oracle"
grand_parent: CLASSES
---

# OracleStatementBuilder

---

This class is the [BaseStatementBuilder](/class/basestatementbuilder)-derived implementation for Oracle. It targets Oracle Database 12c and later, and is automatically registered by [OracleBootstrap](/class/oracle/oraclebootstrap) — you do not need to instantiate it directly under normal use.

## Constructors

```csharp
public OracleStatementBuilder(IDbSetting dbSetting)
public OracleStatementBuilder(IDbSetting dbSetting,
    IResolver<Field, IDbSetting, string> convertFieldResolver = null,
    IResolver<Type, Type> averageableClientTypeResolver = null)
```

`convertFieldResolver` defaults to [OracleConvertFieldResolver](/class/oracle/oracleconvertfieldresolver), used to render `CAST(...)` expressions for typed fields.

## Usability

Use [StatementBuilderMapper](/mapper/statementbuildermapper) to override it with a custom implementation.

```csharp
StatementBuilderMapper.Add(typeof(OracleConnection), new MyCustomOracleStatementBuilder(dbSetting), true);
```
