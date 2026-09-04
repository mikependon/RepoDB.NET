---
layout: default
sidebar: classes
title: "EnterpriseDbGlobalConfiguration"
description: "A class that is being used to initialize the necessary settings for the EnterpriseDb data provider."
permalink: /class/enterprisedb/enterprisedbglobalconfiguration
tags: [repodb, enterprisedbglobalconfiguration, enterprisedb]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EnterpriseDbGlobalConfiguration

---

This class exposes the `UseEnterpriseDb()` extension method of [GlobalConfiguration](/class/globalconfiguration), which wires up all the necessary dependencies for EnterpriseDB (via [EnterpriseDbBootstrap](/class/enterprisedb/enterprisedbbootstrap)).

## Usability

```csharp
GlobalConfiguration
    .Setup()
    .UseEnterpriseDb();
```

{: .note }
> Call this once during application start-up, before opening any `EDBConnection`.
