---
layout: default
sidebar: classes
title: "GlobalConfigurationOptions"
description: "A class that is being used to define the globalized configurations for the application."
permalink: /class/globalconfigurationoptions
tags: [repodb, globalconfiguration]
parent: CLASSES
---

# GlobalConfigurationOptions

---

This class contains the different options that is being used to define a globalized configuration for the library.

#### Properties

- `ConversionType` - a property that is being used to gets or sets the value that defines the conversion logic when converting an instance of `DbDataReader` into a .NET CLR class.
- `DefaultBatchOperationSize` - a property that is being used to gets or sets the default value of the batch operation size. The value defines on this property mainly affects the batch size of the InsertAll, MergeAll and UpdateAll operations.
- `DefaultCacheItemExpirationInMinutes`- a property that is being used to gets or sets the default value of the cache expiration in minutes.
- `EnumDefaultDatabaseType`- a property that is being used to gets or sets the default equivalent `DbType` of an enumeration if it is being used as a parameter to the execution of any non-entity-based operations.
- `KeyColumnReturnBehavior`- a property that is being used to gets or sets the default value of how the push operations (i.e.: [Insert](/operation/insert), [InsertAll](/operation/insertall), [Merge](/operation/merge) and [MergeAll](/operation/mergeall)) behaves when returning the value from the key columns (i.e.: Primary and Identity).

#### Setup

Below is the code used to setup the application using this class.

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