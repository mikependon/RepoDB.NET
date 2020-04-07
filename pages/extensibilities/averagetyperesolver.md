---
layout: navpage
sidebar: extensibilities
title: "Average Type Resolver (RepoDb)"
permalink: /extensibility/averagetyperesolver
tags: [repodb, class, averagetyperesolver, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Average Type Resolver

This object is used within the [BaseStatementBuilder](/class/basestatementbuilder) class as a resolver of which system type to be used for conversion when calling the [Average](/operation/average) and [AverageAll](/operation/averageall) operations.

#### How to Implement?

Simply create a class that implements the [IResolver](/interface/iresolver).

```csharp
public class AverageableTypeResolver : IResolver<Type, Type>
{
    ...
}
```

Then implement the `Resolve()` method like below.

```csharp
public Type Resolve(Type type)
{
    if (type == null)
    {
        throw new NullReferenceException("The type must not be null.");
    }

    // Get the type
    type = type?.GetUnderlyingType();

    // Only convert those numerics
    if (type == typeof(short) ||
        type == typeof(int) ||
        type == typeof(long) ||
        type == typeof(UInt16) ||
        type == typeof(UInt32) ||
        type == typeof(UInt64))
    {
        type = typeof(double);
    }

    // Return the type
    return type;
}
```

#### How to Use?

You have to simply pass it when inheritting the [BaseStatementBuilder](/class/basestatementbuilder) class.

```csharp
internal sealed class OptimizedSqlServerStatementBuilder : BaseStatementBuilder
{
	public OptimizedSqlServerStatementBuilder()
        : base(DbSettingMapper.Get(typeof(SqlConnection)),
            null, // See the ConvertFieldResolver page
            new AverageableTypeResolver())
	{ }
}
```

> Please see the [Statement Builder](/extensibility/statementbuilder) for more information. In addition to this note, there is already an existing resolver that does the same named *RepoDb.Resolvers.ClientTypeToAverageableClientTypeResolver*. It can be used and inheritted immediately.