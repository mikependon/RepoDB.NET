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

An immutable class that holds the definition of the database columns. This class is hugely used internally in the library.

## List of Properties

Below is the list of properties.

| Name | Description |
|:-----|:------------|
| Name | The name of the column.
| IsPrimary | Returns `true` if the column is primary. |
| IsIdentity | Returns `true` if the column is identity. |
| IsNullable | Returns `true` if the column is nullable. |
| Type | The equivalent .NET CLR type. |
| Size | The size of the column from the database. |
| Precision | The precision of the column from the database. |
| Scale | The scale of the column from the database. |
| DatabaseType | The  type of the column from the database. |
| Provider | The database provider who created the instance. |

Below is the way on how to extract the database columns using [IDbHelper](/interface/idbhelper).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var helper = connection.GetDbHelper();
    var dbFields = helper.GetFields(connection, "[dbo].[Person]");
    // Do the stuffs for the 'dbFields' here
}
```

{: .warning }
> We suggest to avoid using this class in any of your development, unless very necessary.