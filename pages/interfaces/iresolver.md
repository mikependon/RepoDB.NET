---
layout: default
sidebar: interfaces
title: "IResolver"
permalink: /interface/iresolver
tags: [repodb, class, iresolver, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Interfaces
---

# IResolver

---

This interface is used to mark a class to be a resolver object. This interface accepts upto the maximum 7 input generic types and 1 output generic type.

In each number of inputted generic types, a method named `Resolve` is given.

### Use-Cases

This interface is used internally by the library, specially in the [BaseStatementBuilder](/class/basestatementbuilder).

Or, you can use it on a situation like:

- Handling the transformation of the column based on the database settings.
- Handling the way on how to correct the inputted value.
  
> The use-case can be unlimitted based your situation.

### How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class ColumnNameResolver : IResolver<string, IDbSetting, string>
{
    public string Resolve(string input, IDbSetting setting)
    {
        return input?.AsField(setting);
    }
}
```

### How to use?

Then you can always use it like this.

```csharp
var setting = DbSettingMapper.Get(typeof(SqlConnection));
var resolver = new ColumnNameResolver();
var fieldName = "Id";
var column = resolver.Resolve(fieldName, setting);

// The value of column would be '[Id]', instead of 'Id'
```

Or, you can pass it on your custom [Statement Builder](/extensibility/statementbuilder).

```csharp
public class OptimizedSqlServerStatementBuilder : BaseStatementBuilder
{
    public OptimizedSqlServerStatementBuilder(IDbSetting dbSetting,
        IResolver<Field, IDbSetting, string> convertFieldResolver)
        : base(dbSetring, convertFieldResolver, null)
    { }
}
```

Please see the [convert field resolver](/extensibility/convertfieldresolver) page for more details.