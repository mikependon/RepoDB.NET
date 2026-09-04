---
layout: default
sidebar: classes
title: "EnterpriseDbConvertFieldResolver"
description: "A class used to resolve the Field name conversion for EnterpriseDB."
permalink: /class/enterprisedb/enterprisedbconvertfieldresolver
tags: [repodb, enterprisedbconvertfieldresolver, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbConvertFieldResolver

---

This class resolves a [Field](/class/field) into a `CAST(column AS TYPE)` SQL fragment whenever the field carries an explicit `.Type`, using [DbTypeToEnterpriseDbStringNameResolver](/class/enterprisedb/dbtypetoenterprisedbstringnameresolver) to determine the EnterpriseDB type name. It is used internally by [EnterpriseDbStatementBuilder](/class/enterprisedb/enterprisedbstatementbuilder) and is not typically used directly.

## Usability

```csharp
var resolver = new EnterpriseDbConvertFieldResolver();
var expression = resolver.Resolve(new Field("Age", typeof(int)), dbSetting);
// CAST("Age" AS INTEGER)
```
