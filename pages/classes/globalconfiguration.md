---
layout: default
sidebar: classes
title: "GlobalConfiguration"
description: "A class that is being used to define the globalized configurations for the library."
permalink: /class/globalconfiguration
tags: [repodb, globalconfiguration]
parent: CLASSES
---

# GlobalConfiguration

---

This class defines global configuration for the library and must be called during application startup.

## Methods

| Name | Description |
|:-----|:------------|
| Setup | Sets up the global configuration. |

## Properties

| Name | Description |
|:-----|:------------|
| Options | The configuration options defined during setup. |

## Setup

Use this method to configure options via the [GlobalConfigurationOptions](/class/globalconfigurationoptions) class.

```csharp
GlobalConfiguration
    .Setup()
    .UseSqlServer();
```

`UseSqlServer()` is an extension method provided by the [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) package. Other extension packages provide their own equivalent methods.

Options can also be specified during setup.

```csharp
GlobalConfiguration
    .Setup(new()
    {
        ConversionType = ConversionType.Default,
        DefaultBatchOperationSize = Constant.DefaultBatchOperationSize,
        DefaultCacheItemExpirationInMinutes = Constant.DefaultCacheItemExpirationInMinutes,
        EnumDefaultDatabaseType = DbType.String,
        KeyColumnReturnBehavior = KeyColumnReturnBehavior.IdentityOrElsePrimary
    })
    .UseSqlServer();
```

{: .note }
> All options are optional. The values shown above are the defaults.
