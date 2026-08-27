---
layout: default
sidebar: classes
title: "FirebirdConvertFieldResolver"
description: "A class used to resolve the Field name conversion for Firebird."
permalink: /class/firebird/firebirdconvertfieldresolver
tags: [repodb, firebirdconvertfieldresolver, firebird]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdConvertFieldResolver

---

This class resolves a [Field](/class/field) into a `CAST(column AS TYPE)` SQL fragment whenever the field carries an explicit `.Type`, using [DbTypeToFirebirdStringNameResolver](/class/firebird/dbtypetofirebirdstringnameresolver) to determine the Firebird type name. It is used internally by [FirebirdStatementBuilder](/class/firebird/firebirdstatementbuilder) and is not typically used directly.

{: .note }
> Firebird's `AVG()` returns a value of the same exact-numeric type as its argument (e.g. `AVG(INTEGER)` is itself `INTEGER`), truncating any fractional part instead of widening to a floating-point type the way MySQL/SQL Server do. [BaseStatementBuilder](/class/basestatementbuilder) already widens exact-numeric field types to `double` before this resolver runs (via `ClientTypeToAverageableClientTypeResolver`), so [Average](/operation/average)/[AverageAll](/operation/averageall) cast to `DOUBLE PRECISION` rather than truncating.

## Usability

```csharp
var resolver = new FirebirdConvertFieldResolver();
var expression = resolver.Resolve(new Field("Age", typeof(int)), dbSetting);
// CAST("Age" AS INTEGER)
```
