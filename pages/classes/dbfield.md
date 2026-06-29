---
layout: default
sidebar: classes
title: "DbField"
description: "A class that contains the necessary properties that defines a database field."
permalink: /class/dbfield
tags: [repodb, dbfield]
parent: CLASSES
---

# DbField

---

An immutable class that holds the definition of a database column. Used extensively by the library internally.

## List of Properties

| Name | Description |
|:-----|:------------|
| Name | The column name.
| IsPrimary | Returns `true` if the column is primary. |
| IsIdentity | Returns `true` if the column is identity. |
| IsNullable | Returns `true` if the column is nullable. |
| Type | The equivalent .NET CLR type. |
| Size | The column size. |
| Precision | The column precision. |
| Scale | The column scale. |
| DatabaseType | The database type of the column. |
| Provider | The database provider that created the instance. |

The following example extracts database columns using [IDbHelper](/interface/idbhelper).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var helper = connection.GetDbHelper();
    var dbFields = helper.GetFields(connection, "[dbo].[Person]");
    // Do the stuffs for the 'dbFields' here
}
```

{: .warning }
> Avoid using this class directly in application code unless strictly necessary.
