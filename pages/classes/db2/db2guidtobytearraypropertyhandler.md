---
layout: default
sidebar: classes
title: "Db2GuidToByteArrayPropertyHandler"
description: "A property handler that converts a Guid data entity property to/from a byte array for binding against a Db2 CHAR(16) FOR BIT DATA column."
permalink: /class/db2/db2guidtobytearraypropertyhandler
tags: [repodb, db2guidtobytearraypropertyhandler, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2GuidToByteArrayPropertyHandler

---

Db2 has no native GUID/UNIQUEIDENTIFIER type. The idiomatic Db2 storage for a GUID is a fixed-length 16-byte `CHAR(16) FOR BIT DATA` column.

This [IPropertyHandler](/interface/ipropertyhandler) converts a `Guid` entity property to/from a `byte[]` for binding against such a column.

{: .note }
> It is intentionally **not** registered automatically for every `Guid` property, since [PropertyHandlerMapper](/mapper/propertyhandlermapper) registrations keyed by CLR type are global across the whole process — auto-registering it would also affect unrelated connections. Register it explicitly, scoped to the specific entity property that maps to a `CHAR(16) FOR BIT DATA` column, especially if your process also uses another RepoDb provider that handles `Guid` natively.

## Usability

```csharp
PropertyHandlerMapper.Add<CompleteTable, Guid>(
    e => e.SessionId, new Db2GuidToByteArrayPropertyHandler(), true);
```
