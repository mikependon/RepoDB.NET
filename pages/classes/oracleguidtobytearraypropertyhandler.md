---
layout: default
sidebar: classes
title: "OracleGuidToByteArrayPropertyHandler"
description: "A property handler that converts a Guid data entity property to/from a byte array for binding against an Oracle RAW(16) column."
permalink: /class/oracleguidtobytearraypropertyhandler
tags: [repodb, oracleguidtobytearraypropertyhandler, oracle]
parent: CLASSES
---

# OracleGuidToByteArrayPropertyHandler

---

Oracle has no native GUID/UNIQUEIDENTIFIER type, and ODP.NET's `OracleParameter.Value` setter does not accept a raw `Guid` value — it throws `ArgumentException: Value does not fall within the expected range.` if one is assigned directly. The idiomatic Oracle storage for a GUID is a 16-byte `RAW(16)` column.

This [IPropertyHandler](/interface/ipropertyhandler) converts a `Guid` entity property to/from a `byte[]` for binding against such a column.

{: .note }
> It is intentionally **not** registered automatically for every `Guid` property, since [PropertyHandlerMapper](/mapper/propertyhandlermapper) registrations keyed by CLR type are global across the whole process — auto-registering it would also affect unrelated connections. Register it explicitly, scoped to the specific entity property that maps to a `RAW(16)` column.

## Usability

```csharp
PropertyHandlerMapper.Add<CompleteTable, Guid>(
    e => e.SessionId, new OracleGuidToByteArrayPropertyHandler(), true);
```
