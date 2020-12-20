---
layout: navpage
sidebar: classes
title: "DbField"
description: "A class that contains the necessary properties that defines a database field."
permalink: /class/dbfield
tags: [repodb, class, dbfield, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# DbField

An immutable class that holds the definition of the database columns. This class is hugely used internally in the library.

###### List of Properties

- `Name` - the name of the column.
- `IsPrimary` - returns `true` if the column is primary.
- `IsIdentity` - returns `true` if the column is identity.
- `IsNullable` - returns `true` if the column is nullable.
- `Type` - the equivalent .NET CLR type.
- `Size` - the size of the column from the database.
- `Precision` - the precision of the column from the database.
- `Scale` - the scale of the column from the database.
- `DatabaseType` - tye type of the column from the database.

###### How to Extract?

Below is the way on how to extract the database columns using [IDbHelper](/interface/idbhelper).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var helper = connection.GetDbHelper();
    var dbFields = helper.GetFields(connection, "[dbo].[Person]");
    // Do the stuffs for the 'dbFields' here
}
```

> We suggest that you should not instantiate an instance of this class in any of your development, unless very necessary.