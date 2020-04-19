---
layout: navpage
sidebar: classes
title: "BaseDbSetting"
permalink: /class/basedbsetting
tags: [repodb, class, basedbsetting, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# BaseDbSetting

This class stands as the base class of all [IDbSetting](/interface/idbsetting)-based classes.

#### Use-Cases

To simplify your implementation, always use this class over [IDbSetting](/interface/idbsetting) when overriding the default database settings.

#### How to Implement?

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

#### How to Use?

Simply use the [DbSettingMapper](/mapper/dbsettingmapper) class to map it to the specific RDBMS data provider.

```csharp
DbSettingMapper.Add(typeof(SqlConnection), new MyCustomSqlServerDbSetting(), true);
```