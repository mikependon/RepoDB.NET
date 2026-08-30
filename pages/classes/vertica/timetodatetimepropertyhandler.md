---
layout: default
sidebar: classes
title: "TimeToDateTimePropertyHandler"
description: "A property handler that re-bases the date component of a value read back from a Vertica TIME column."
permalink: /class/vertica/timetodatetimepropertyhandler
tags: [repodb, timetodatetimepropertyhandler, vertica]
parent: "Vertica"
grand_parent: CLASSES
---

# TimeToDateTimePropertyHandler

---

Vertica's driver returns a `TIME` column's value combined with today's date rather than a fixed placeholder date. This [IPropertyHandler](/interface/ipropertyhandler) re-bases the date component of a value read back from a `TIME` column onto `DateTime`'s default (`0001-01-01`) date, keeping only its time-of-day. `Set` passes the value through unchanged — Vertica only stores the time-of-day portion of a bound value in a `TIME` column regardless of its date component.

{: .important }
> This class lives in the `RepoDb.PropertyHandlers.Vertica` namespace. A second, near-identical class named `VerticaTimeToDateTimePropertyHandler` also exists in the plain `RepoDb.PropertyHandlers` namespace — it is not referenced anywhere in the library or its test suite and appears to be leftover duplicate code from a refactor. Prefer `TimeToDateTimePropertyHandler` (this class), which is the one the library's own integration tests register.

## Usability

Register it explicitly, scoped to the specific entity property that maps to a `TIME` column.

```csharp
PropertyHandlerMapper.Add<Person, DateTime>(
    e => e.StartTime, new RepoDb.PropertyHandlers.Vertica.TimeToDateTimePropertyHandler(), true);
```
