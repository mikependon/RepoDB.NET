---
layout: page
title: "Convert Field Resolver (RepoDb)"
permalink: /extensibility/convertfieldresolver
tags: [repodb, class, convertfieldresolver, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Convert Field Resolver

This object is used within the [BaseStatementBuilder](/class/basestatementbuilder) class as a resolver when converting a column to a specific type.

#### How to Implement?

Simply create a class that implements the [IResolver](/interface/iresolver). The generic types must be of type [Field](/class/field) and [DbSetting](/class/dbsetting). The return type must be of string.

```csharp
public class SqlServerConvertFieldResolver : IResolver<Field, IDbSetting, string>
{
    ...
}
```

Then implement the `Resolve()` method like below.

```csharp
public string Resolve(Field field,
    IDbSetting dbSetting)
{
    if (field != null && field.Type != null)
    {
        var dbType = GetDbTypeFromSystemType(field.Type); // We recommend that you create an additional resolver called 'DbTypeResolver'
        if (dbType != null)
        {
            var dbTypeName = GetStringNameFromDbType(dbType); // We recommend that you create an additional resolver called 'StringNameResolver'
            return string.Concat("CAST(", field.Name.AsField(dbSetting), " AS ", dbTypeName.AsQuoted(dbSetting), ")");
        }
    }
    return field?.Name?.AsQuoted(true, true, dbSetting);
}
```

#### How to Use?

You have to simply pass it when inheritting the [BaseStatementBuilder](/class/basestatementbuilder) class.

```csharp
internal sealed class OptimizedSqlServerStatementBuilder : BaseStatementBuilder
{
    public OptimizedSqlServerStatementBuilder()
        : base(DbSettingMapper.Get(typeof(SqlConnection)),
            new SqlServerConvertFieldResolver())
    { }
}
```

> Please see the [Statement Builder](/extensibility/statementbuilder) for more information.