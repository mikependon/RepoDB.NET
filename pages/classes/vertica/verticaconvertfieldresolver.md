---
layout: default
sidebar: classes
title: "VerticaConvertFieldResolver"
description: "A class used to resolve the Field name conversion for Vertica."
permalink: /class/vertica/verticaconvertfieldresolver
tags: [repodb, verticaconvertfieldresolver, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaConvertFieldResolver

---

This class resolves a [Field](/class/field) into a `CAST(column AS TYPE)` SQL fragment whenever the field carries an explicit `.Type`, using [DbTypeToVerticaStringNameResolver](/class/vertica/dbtypetoverticastringnameresolver) to determine the Vertica type name. It is used internally by [VerticaStatementBuilder](/class/vertica/verticastatementbuilder) and is not typically used directly.

## Usability

```csharp
var resolver = new VerticaConvertFieldResolver();
var expression = resolver.Resolve(new Field("Age", typeof(int)), dbSetting);
// CAST("Age" AS INTEGER)
```
