---
layout: default
sidebar: interfaces
title: "IDbSetting"
permalink: /interface/idbsetting
tags: [repodb, idbsetting]
parent: INTERFACES
---

# IDbSetting

---

This interface is used to mark a class to be a database setting object. It allows you to control the behavior of the library on a specific RDBMS data provider based on the value you provided on the properties this class.

## Properties

Below is the list of properties.

| Name | Description |
|:-----|:------------|
| AreTableHintsSupported | Gets the value that indicates whether the table hints are supported. |
| ClosingQuote | Gets the character used for closing quote. |
| AverageableType | Gets the default averageable .NET CLR types for the database. |
| DefaultSchema | Gets the default schema of the database. |
| IsDirectionSupported | Gets a value that indicates whether setting the value of `DbParameter.Direction` object is supported. |
| IsExecuteReaderDisposable | Gets a value that indicates whether the `DbCommand` object must be disposed after calling the `DbCommand.ExecuteReader()` method. |
| IsMultiStatementExecutable | Gets a value whether the multiple statement execution is supported. |
| IsPreparable | Gets a value that indicates whether the current DB Provider supports the `DbCommand.Prepare()` calls. |
| IsUseUpsert | Gets a value that indicates whether the [Insert](/operation/insert)/[Update](/operation/update) operation will be used for [Merge](/operation/merge) operation. |
| OpeningQuote | Gets the character used for opening quote. |
| ParameterPrefix | Gets the character used for the database command parameter prefixing. |

## How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class MyCustomSqlServerDbSetting : IDbSetting
{
    public bool AreTableHintsSupported { get; set; } = true;
    public string ClosingQuote { get; set; } = "]";
    public Type AverageableType { get; set; } = typeof(double);
    public string DefaultSchema { get; set; } = "dbo";
    public bool IsDirectionSupported { get; set; } = true;
    public bool IsExecuteReaderDisposable { get; set; } = true;
    public bool IsMultiStatementExecutable { get; set; } = true;
    public bool IsPreparable { get; set; } = true;
    public bool IsUseUpsert { get; set; } = false;
    public string OpeningQuote { get; set; } = "[";
    public string ParameterPrefix { get; set; } = "@";
}
```

Please see the more detailed implementations at [Database Setting](/extensibility/databasesetting) page.

## GetHashCode

You have to override the implementation of the `GetHashCode()` based on the combinations of the properties.

The reason to this, the library is using the equality based on the generated hashcode. Failure to comply may trigger a performance problem in your application.

> We recommend to instead use the [BaseDbSetting](/class/basedbsetting) class when implementing a customized database setting.

## Usability

Once the class has been implemented, you have to call the [DbSettingMapper](/mapper/dbsettingmapper) class for mappings (per RDBMS data provider).

```csharp
DbSettingMapper.Add(typeof(SqlConnection), new CustomSqlServerDbSetting(), true);
```