---
layout: default
sidebar: classes
title: "OracleConvertFieldResolver"
description: "A class used to resolve the Field name conversion for Oracle."
permalink: /class/oracleconvertfieldresolver
tags: [repodb, oracleconvertfieldresolver, oracle]
parent: CLASSES
---

# OracleConvertFieldResolver

---

This class resolves a [Field](/class/field) into a `CAST(column AS TYPE)` SQL fragment whenever the field carries an explicit `.Type`, using [DbTypeToOracleStringNameResolver](/class/dbtypetooraclestringnameresolver) to determine the Oracle type name. It is used internally by [OracleStatementBuilder](/class/oraclestatementbuilder) and is not typically used directly.

## Usability

```csharp
var resolver = new OracleConvertFieldResolver();
var expression = resolver.Resolve(new Field("Age", typeof(int)), dbSetting);
// CAST("Age" AS NUMBER(10))
```
