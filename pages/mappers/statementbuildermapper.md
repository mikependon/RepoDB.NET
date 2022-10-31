---
layout: default
sidebar: mappers
title: "StatementBuilderMapper"
description: "A mapper class for all the statement builder classes. The mapping can be made based on the type of the target RDBMS data provider."
permalink: /mapper/statementbuildermapper
tags: [repodb, statementbuildermapper]
parent: MAPPERS
---

# StatementBuilderMapper

---

A mapper class for the [IStatementBuilder](/interface/istatementbuilder)-based class. The mapping can be made based on the type of the target RDBMS data provider. Please visit the [Database Setting](/extensibility/databasesetting) for more information.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Add | Adds a mapping between the [IStatementBuilder](/interface/istatementbuilder) and the type of the `DbConnection`. |
| Clear | Clears all the existing mappings of the statement builders. |
| Get | Gets the existing mapped [IStatementBuilder](/interface/istatementbuilder) object based on the type of the `DbConnection`. |
| Remove | Removes the existing mapped [IStatementBuilder](/interface/istatementbuilder) object based on the type of the `DbConnection`. |

## Usability

To add a mapping, simply call the `Add()` method.

```csharp
StatementBuilderMapper.Add<SqlConnection>(new OptimizedSqlServerStatementBuilder(), true);
```

{: .warning }
> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get()` method.

```csharp
var statementBuilder = StatementBuilderMapper.Get<SqlConnection>();
```

To remove the mapping, use the `Remove()` method.

```csharp
StatementBuilderMapper.Remove<SqlConnection>();
```

