---
layout: default
sidebar: extensibilities
title: "Database Helper"
permalink: /extensibility/databasehelper
tags: [repodb, databasehelper]
parent: EXTENSIBILITIES
---

# Database Helper

---

A database helper is an object that is used by the library as a helper class for a specific RDBMS data provider. It is a part of the extensibility feature in order for you (as a developer) to override the default implementation of the library, specially when it comes to optimizing the database fields extraction.

You have use the [IDbHelper](/interface/idbhelper) interface as the contract for the implementation and [DbHelperMapper](/mapper/dbhelpermapper) class for the mapping.

### Pre-requisites

- The naming must be `Desired Name` + `DbHelper` (i.e.: `SqlServerDbHelper`).
- The implementation must be `internal sealed`.
- The namespace must be `RepoDb.DbHelpers`.
- The class must be residing inside the `DbHelpers` folder.

> Please see the [folder structuring](/extensibility/folderstructuring) page.

### Get Started

First, you have to manually create a class that implements the [IDbHelper](/interface/idbhelper) interface.

Let us say, you are targeting to create a database helper for SQL Server.

```csharp
internal sealed class OptimizedSqlServerDbHelper : IDbHelper
{
    public IEnumerable<DbField> GetFields(IDbConnection connection,
        string tableName,
        IDbTransaction transaction = null)
    {
        // Implementations for the GetFields() here
    }

    public async Task<IEnumerable<DbField>> GetFieldsAsync(IDbConnection connection,
        string tableName,
        IDbTransaction transaction = null)
    {
        // Implementations for the GetFieldsAsync() here
    }

    public object GetScopeIdentity(IDbConnection connection,
        IDbTransaction transaction = null)
    {
        // Implementations for the GetScopeIdentity() here
    }

    public async Task<object> GetScopeIdentityAsync(IDbConnection connection,
        IDbTransaction transaction = null)
    {
        // Implementations for the GetScopeIdentityAsync() here
    }
}
```

> You have to implement all the methods needed by this interface. With this, you have the full control of you helper object.

### Method GetFields

This method is returning the list of [DbField](/class/dbfield) objects. Below are the sample implementations for SQL Server.

First, create the helper methods to extract the schema.

```csharp
private string GetSchema(string tableName,
    IDbSetting dbSetting)
{
    if (tableName.IndexOf(dbSetting.SchemaSeparator) > 0)
    {
        var splitted = tableName.Split(dbSetting.SchemaSeparator.ToCharArray());
        return splitted[0].AsUnquoted(true, dbSetting);
    }
    return dbSetting.DefaultSchema;
}
```

Then, a method to extract the tablename.

```csharp
private string GetTableName(string tableName,
    IDbSetting dbSetting)
{
    if (tableName.IndexOf(dbSetting.SchemaSeparator) > 0)
    {
        var splitted = tableName.Split(dbSetting.SchemaSeparator.ToCharArray());
        return splitted[1].AsUnquoted(true, dbSetting);
    }
    return tableName.AsUnquoted(true, dbSetting);
}
```

Then, a method that returns a SQL text.

```csharp
private string GetCommandText()
{
    return @"
        SELECT C.COLUMN_NAME AS ColumnName
            , CONVERT(BIT, COALESCE(TC.is_primary, 0)) AS IsPrimary
            , CONVERT(BIT, COALESCE(TMP.is_identity, 1)) AS IsIdentity
            , CONVERT(BIT, COALESCE(TMP.is_nullable, 1)) AS IsNullable
            , C.DATA_TYPE AS DataType
            , CONVERT(INT, COALESCE(TMP.max_length, 1)) AS Size
            , CONVERT(TINYINT, COALESCE(TMP.precision, 1)) AS Precision
            , CONVERT(TINYINT, COALESCE(TMP.scale, 1)) AS Scale
        FROM INFORMATION_SCHEMA.COLUMNS C
        OUTER APPLY
        (
            SELECT 1 AS is_primary
            FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU
            LEFT JOIN INFORMATION_SCHEMA.TABLE_CONSTRAINTS TC
                ON TC.TABLE_SCHEMA = C.TABLE_SCHEMA
                AND TC.TABLE_NAME = C.TABLE_NAME
                AND TC.CONSTRAINT_NAME = KCU.CONSTRAINT_NAME
            WHERE KCU.TABLE_SCHEMA = C.TABLE_SCHEMA
                AND KCU.TABLE_NAME = C.TABLE_NAME
                AND KCU.COLUMN_NAME = C.COLUMN_NAME
                AND TC.CONSTRAINT_TYPE = 'PRIMARY KEY'
        ) TC 
        OUTER APPLY
        (
            SELECT SC.name
                , SC.is_identity
                , SC.is_nullable
                , SC.max_length
                , SC.scale
                , SC.precision
            FROM [sys].[columns] SC
            INNER JOIN [sys].[tables] ST ON ST.object_id = SC.object_id
            WHERE SC.name = C.COLUMN_NAME
                AND ST.name = C.TABLE_NAME
        ) TMP
        WHERE
            C.TABLE_SCHEMA = @Schema
            AND C.TABLE_NAME = @TableName;";
}
```

Then, a method that converts the instance of the `DbDataReader` into a [DbField](/class/dbfield) object.

```csharp
private DbField ReaderToDbField(IDataReader reader)
{
    return new DbField(reader.GetString(0),
        reader.IsDBNull(1) ? false : reader.GetBoolean(1),
        reader.IsDBNull(2) ? false : reader.GetBoolean(2),
        reader.IsDBNull(3) ? false : reader.GetBoolean(3),
        reader.IsDBNull(4) ? DbTypeResolver.Resolve("text") : DbTypeResolver.Resolve(reader.GetString(4)),
        reader.IsDBNull(5) ? 0 : GetConvertedSize(reader.GetString(4), reader.GetInt32(5)),
        reader.IsDBNull(6) ? (byte?)0 : reader.GetByte(6),
        reader.IsDBNull(7) ? (byte?)0 : reader.GetByte(7),
        reader.IsDBNull(7) ? "text" : reader.GetString(4),
        "SQLSVR");
}

private int? GetConvertedSize(string type, int size)
{
    return (size == -1) ? 8000 : size;
}
```

Lastly, call everything on within the `GetFields()` method.

```csharp
public IEnumerable<DbField> GetFields(IDbConnection connection,
    string tableName,
    IDbTransaction transaction = null)
{
    var commandText = GetCommandText();
    var setting = connection.GetDbSetting();
    var param = new
    {
        Schema = GetSchema(tableName, setting),
        TableName = GetTableName(tableName, setting)
    };

    using (var reader = connection.ExecuteReader(commandText, param, transaction: transaction))
    {
        var dbFields = new List<DbField>();
        while (reader.Read())
        {
            dbFields.Add(ReaderToDbField(reader));
        }
        return dbFields;
    }
}
```

### Method GetScopedIdentity

This method is returning the latest generated identity from the database (scoped level). If not present at the scoped level, then it will return the global level. Below is a sample implementation.

Simply call the `SCOPE_IDENTITY` and `@@IDENTITY` function of SQL Server.

```csharp
public object GetScopeIdentity(IDbConnection connection,
    IDbTransaction transaction = null)
{
    return connection.ExecuteScalar("SELECT COALESCE(SCOPE_IDENTITY(), @@IDENTITY);");
}
```

### Usability

Once you have the database helper implemented in a customized class, you can use the [DbHelperMapper](/mapper/dbhelpermapper) class to map it in your target RDDMS data provider.

```csharp
DbHelperMapper.Add(typeof(SqlConnection), new OptimizedSqlServerDbHelper(), true);
```

The library will then use your customized database helper when you are calling the extended [methods](/docs#methods) of the `SqlConnection` object.

### How to Retrieve?

You can use the [DbHelperMapper](/mapper/dbhelpermapper) to get the database helper by connection type.

```csharp
var helper = DbHelperMapper.Get<SqlConnection>();
// Use the 'helper' here
```

Or, you can use the `GetDbHelper()` extended method.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var helper = connection.GetDbHelper();
    // Use the 'helper' here
}
```