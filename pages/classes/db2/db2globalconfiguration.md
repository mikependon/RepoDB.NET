---
layout: default
sidebar: classes
title: "Db2GlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the Db2 data provider."
permalink: /class/db2/db2globalconfiguration
tags: [repodb, db2globalconfiguration, db2]
parent: "Db2"
grand_parent: CLASSES
---

# Db2GlobalConfiguration

---

This class exposes the `UseDb2()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for Db2 (via [Db2Bootstrap](/class/db2/db2bootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseDb2();
```

{: .note }
> Call this once during application start-up, before opening any `DB2Connection`.
