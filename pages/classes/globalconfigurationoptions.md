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

This class contains the options used to define global configuration for the library.

## Properties

| Name | Description |
|:-----|:------------|
| ConversionType | Defines the conversion logic when mapping a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) to a .NET CLR class. |
| DefaultBatchOperationSize | The default batch size for InsertAll, MergeAll, and UpdateAll operations. |
| DefaultCacheItemExpirationInMinutes | The default cache expiration in minutes. |
| EnumDefaultDatabaseType | The default [DbType](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0) for enumerations used as parameters in non-entity-based operations. |
| KeyColumnReturnBehavior | Defines how push operations ([Insert](/operation/insert), [InsertAll](/operation/insertall), [Merge](/operation/merge), [MergeAll](/operation/mergeall)) return values from key columns (Primary and Identity). 

## Setup

```csharp
var options = new GlobalConfigurationOptions()
{
    ConversionType = ConversionType.Default,
    DefaultBatchOperationSize = Constant.DefaultBatchOperationSize,
    DefaultCacheItemExpirationInMinutes = Constant.DefaultCacheItemExpirationInMinutes,
    EnumDefaultDatabaseType = DbType.String,
    KeyColumnReturnBehavior = KeyColumnReturnBehavior.IdentityOrElsePrimary
};
GlobalConfiguration.Setup(options);
```

{: .note }
> All options are optional. The values shown above are the defaults.
