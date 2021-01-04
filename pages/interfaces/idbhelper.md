---
layout: default
sidebar: interfaces
title: "IDbHelper"
permalink: /interface/idbhelper
tags: [repodb, class, idbhelper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: INTERFACES
---

# IDbHelper

---

This interface is used to mark a class to be a database helper object. It is very useful if you would like optimize and override the default implementation of the library (i.e.: retrieving the list of fields, retrieving newly generated identity).

### Methods

Below are the methods available from this interface.

- `GetFields` - gets the list of [DbField](/class/dbfield) objects from the database.
- `GetScopedIdentity` - gets the newly generated identity from the database.

### How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class OptimizedSqlServerDbHelper : IDbHelper
{
    public IEnumerable<DbField> GetFields(IDbConnection connection,
        string tableName,
        IDbTransaction transaction = null)
    {
        // Implementations for the GetFields() here
    }

    public object GetScopeIdentity(IDbConnection connection,
        IDbTransaction transaction = null)
    {
        // Implementations for the GetScopeIdentity() here
    }

    ...
}
```

Please see the more detailed implementations at [Database Helper](/extensibility/databasehelper) page.

> You have to implement all the methods needed by this interface. With this, you have the full control of you helper object.

### How to use?

Once you have the database helper implemented in a customized class, you can use the [DbHelperMapper](/mapper/dbhelpermapper) class to map it in your target RDDMS data provider.

```csharp
DbHelperMapper.Add(typeof(SqlConnection), new OptimizedSqlServerDbHelper(), true);
```

The library will then use your customized database helper in most of the extended [methods](/docs#methods) of the `DbConnection` object.