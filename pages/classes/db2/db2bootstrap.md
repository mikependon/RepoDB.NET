---
layout: default
sidebar: classes
title: "Db2Bootstrap"
description: "A class that is being used to initialize the necessary settings for the DB2Connection object."
permalink: /class/db2/db2bootstrap
tags: [repodb, db2bootstrap, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2Bootstrap

---

This class initializes the necessary dependencies for the `DB2Connection` object — the [DbSetting](/class/db2/db2dbsetting), [DbHelper](/class/db2/db2dbhelper) and [StatementBuilder](/class/db2/db2statementbuilder) — and registers them via their respective mappers.

## Properties

| Name | Description |
|:-----|:------------|
| IsInitialized | Returns `true` once the initialization has completed. |

## Usability

Call [Db2GlobalConfiguration.UseDb2()](/class/db2/db2globalconfiguration) during application start-up; it triggers this class internally.

```csharp
GlobalConfiguration
    .Setup()
    .UseDb2();
```

{: .note }
> Initialization is a one-time, idempotent operation — calling `UseDb2()` more than once has no additional effect.
