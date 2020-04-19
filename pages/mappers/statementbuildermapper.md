---
layout: navpage
sidebar: mappers
title: "StatementBuilderMapper"
permalink: /mapper/statementbuildermapper
tags: [repodb, class, statementbuildermapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# StatementBuilderMapper

A mapper class for the [IStatementBuilder](/interface/istatementbuilder)-based class. The mapping can be made based on the type of the target RDBMS data provider. Please visit the [Database Setting](/extensibility/databasesetting) for more information.

#### Methods

Below are the methods available from this class.

- `Add` - adds a mapping between the [IStatementBuilder](/interface/istatementbuilder) and the type of the `DbConnection`.
- `Clear` - clears all the mappings for the statement builders.
- `Get` - gets the mapped [IStatementBuilder](/interface/istatementbuilder) based on the type of the `DbConnection`.
- `Remove` - removed the mapping between the [IStatementBuilder](/interface/istatementbuilder) and the type of the `DbConnection`.

#### Use-Cases

You should use this class if you would like to override the default mapping of the library when it comes to database statement building.

#### How to Use?

To add a mapping, simply call the `Add` method.

```csharp
StatementBuilderMapper.Add<SqlConnection>(new OptimizedSqlServerStatementBuilder(), true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get` method.

```csharp
var statementBuilder = StatementBuilderMapper.Get<SqlConnection>();
```

To remove the mapping, use the `Remove` method.

```csharp
StatementBuilderMapper.Remove<SqlConnection>();
```

