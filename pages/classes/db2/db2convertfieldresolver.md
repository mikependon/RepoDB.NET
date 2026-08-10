---
layout: default
sidebar: classes
title: "Db2ConvertFieldResolver"
description: "A class used to resolve the Field name conversion for Db2."
permalink: /class/db2/db2convertfieldresolver
tags: [repodb, db2convertfieldresolver, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2ConvertFieldResolver

---

This class resolves a [Field](/class/field) into a `CAST(column AS TYPE)` SQL fragment whenever the field carries an explicit `.Type`, using [DbTypeToDb2StringNameResolver](/class/db2/dbtypetodb2stringnameresolver) to determine the Db2 type name. It is used internally by [Db2StatementBuilder](/class/db2/db2statementbuilder) and is not typically used directly.

## Usability

```csharp
var resolver = new Db2ConvertFieldResolver();
var expression = resolver.Resolve(new Field("Age", typeof(int)), dbSetting);
// CAST("Age" AS INTEGER)
```
