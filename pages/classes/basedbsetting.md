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

This class stands as the base class of all [IDbSetting](/interface/idbsetting)-based classes.

## Use-Cases

To simplify your implementation, always use this class over the [IDbSetting](/interface/idbsetting) interface when overriding the default database settings.

## How to Implement?

Simply create a class that inherits this class, then set the properties in the constructor.

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

> Additional benefits when using this class, you do not need to implement the `GetHashCode()` method as it is already implemented within the base class.

## Usability

Simply use the [DbSettingMapper](/mapper/dbsettingmapper) class to map it to a specific RDBMS data provider.

```csharp
DbSettingMapper.Add(typeof(SqlConnection), new MyCustomSqlServerDbSetting(), true);
```