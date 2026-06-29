---
layout: default
sidebar: classes
title: "BaseDbSetting"
description: "A base class for all the database setting classes."
permalink: /class/basedbsetting
tags: [repodb, basedbsetting]
parent: CLASSES
---

# BaseDbSetting

---

This class is the base class of all [IDbSetting](/interface/idbsetting)-based classes.

## Use-Cases

Use this class instead of the [IDbSetting](/interface/idbsetting) interface when overriding default database settings.

## How to Implement?

Create a class that inherits this class and set the properties in the constructor.

```csharp
public sealed class MyCustomSqlServerDbSetting : BaseDbSetting
{
    public MyCustomSqlServerDbSetting()
        : base()
    {
        AreTableHintsSupported = true;
        AverageableType = typeof(double);
        ClosingQuote = "]";
        DefaultSchema = "dbo";
        IsDirectionSupported = true;
        IsExecuteReaderDisposable = true;
        IsMultiStatementExecutable = true;
        IsPreparable = true;
        IsUseUpsert = false;
        OpeningQuote = "[";
        ParameterPrefix = "@";
        SchemaSeparator = ".";
    }
}
```

{: .note }
> This class already implements `GetHashCode()`, so no override is required.

## Usability

Use the [DbSettingMapper](/mapper/dbsettingmapper) class to map the setting to a specific RDBMS provider.

```csharp
DbSettingMapper.Add(typeof(SqlConnection), new MyCustomSqlServerDbSetting(), true);
```
