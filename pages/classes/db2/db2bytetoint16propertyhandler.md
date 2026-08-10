---
layout: default
sidebar: classes
title: "Db2ByteToInt16PropertyHandler"
description: "A property handler that converts a byte data entity property to/from a short for binding against a Db2 SMALLINT column."
permalink: /class/db2/db2bytetoint16propertyhandler
tags: [repodb, db2bytetoint16propertyhandler, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2ByteToInt16PropertyHandler

---

Db2 has no native single-byte integer type — the smallest Db2 integer type is `SMALLINT` (a 16-bit signed integer), which `IBM.Data.Db2` surfaces to .NET as `short`. A `byte` entity property therefore cannot be bound directly against a `SMALLINT` column.

This [IPropertyHandler](/interface/ipropertyhandler) converts a `byte` entity property to/from a `short` for binding against such a column.

{: .note }
> Like [Db2GuidToByteArrayPropertyHandler](/class/db2/db2guidtobytearraypropertyhandler), it is intentionally **not** registered automatically for every `byte` property — register it explicitly, scoped to the specific entity property that maps to a `SMALLINT` column.

## Usability

```csharp
PropertyHandlerMapper.Add<CompleteTable, byte>(
    e => e.ColumnByte, new Db2ByteToInt16PropertyHandler(), true);
```
