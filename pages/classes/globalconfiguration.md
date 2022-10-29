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

This class is used to define a globalized configuration for the library. This class must be called and setup during the startup.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Setup | A method that is used to setup the configurations. |

## Properties

Below is the list of properties.

| Name | Description |
|:-----|:------------|
| Options | The instance that contains the configurations defined during the setup. |

## Setup

Below is the code used to setup the application. This method is used to define the configuration options via the [GlobalConfigurationOptions](/class/globalconfigurationoptions) class.

```csharp
GlobalConfiguration
    .Setup()
    .UseSqlServer();
```

The `UseSqlServer()` method is an extended method that is provided by the extended package [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer). The other extended package has its own extension.

The options can also be defined during the configurations.

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

**Note:** All the properties defined in the options are optional. The one defined above are the default values.