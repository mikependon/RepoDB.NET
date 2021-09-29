---
layout: post
title: "PropertyValueAttribute - A Setting for IDbDataParameter"
author: "Michael Camara Pendon"
date: 2021-09-29 06:00:00 +0200
categories: blogs repodb
---

### TL;DR

We are very excited to announce the availability of the [PropertyValueAttribute](/attribute/propertyvalue) object. It enables you (as a user of the library) to set the property of the `IDbDataParameter` object before the actual execution against the database.

### Implicit Mapping

RepoDB allows you to fluently map a property into its destination object from the database (i.e.: TableName, ViewName and ColumnName).

> The main goal is to eliminate the decoration of the attributes on your entity model (and property) so it is decoupled from the ORM framework.

Therefore, you are not limited on this anymore.

```csharp
[Map("[dbo].[Person]")]
public class Person
{
    [Map("FName")]
    public string FirstName { get; set; }

    ...
}
```

But instead, do this.

```csharp
FluentMapper
    .Entity<Person>()
    .Table("[dbo].[Person]")
    .Column(e => e.FirstName, "FName");
```

However, the supports are limited only to the following objects.

- TableName/ViewName
- ColumnName
- DbType
- Identity/Primary
- ClassHandler/PropertyHandler

### Explicit Attribute

Before the release of [RepoDb v1.12.9](https://www.nuget.org/packages/RepoDb/1.12.9), there is no way we can override the value being passed to the properties of the `IDbDataParameter` object (i.e.: `Size`, `Precision` and `Scale`). In the recent release, we had introduced various attributes that are addressing this need.

As a sample, the [SizeAttribute](/attribute/parameter/size) object, which inheritted the [PropertyValueAttribute](/attribute/parameter/propertyvalueattribute) object, it is used to explicitly set the value of the `Size` property of the `IDbDataParameter` object before the actual execution against the database.

See below the explicit declaration of this attribute.

```csharp
public class Person
{
    [Size(256)]
    public string FirstName { get; set; }

    ...
}
```

And its equivalent implicit mapping.

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.FirstName, new SizeAttribute(256));
```

We can as well map multiple attributes together (in one go).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.FirstName, new PropertyValueAttribute[]
    {
        new NameAttribute("FName"),
        new SizeAttribute(256),
        new DbTypeAttribute(DbType.NVarChar)
    });
```

The other attributes that were introduced are the following:

- [DbType](/attribute/parameter/dbtype)
- [Direction](/attribute/parameter/direction)
- [IsNullable](/attribute/parameter/isnullable)
- [Name](/attribute/parameter/name)
- [Precision](/attribute/parameter/precision)
- [Scale](/attribute/parameter/scale)
- [Size](/attribute/parameter/size)

### PropertyValueAttribute

What makes it more exciting is the _dynamic capability_ of the [PropertyValueAttribute](/attribute/parameter/propertyvalueattribute) object.  This object is designed to be more extensible to cater all the scenarios pertaining to the setting of the properties of the `IDbDataParameter` object.

The constructor has 3 arguments and are required to be called from the derived classes.

- ParameterType - the type of the parameter object.
- PropertyName - the name of the target property from the parameter object.
- Value - the actual value to be set.

Below is a sample customized class you can create to set the `NpgsqlDbType` property of the `NpgsqlParameter`.

```csharp
public class MyCustomizedNpgsqlDbTypeAttribute : PropertyValueAttribute
{
    public MyCustomizedNpgsqlDbTypeAttribute(NpgsqlDbType type)
        : base(typeof(NpgsqlParameter), nameof(NpgsqlParameter.NpgsqlDbType), type)
    { }
}
```

And use it as your decorative attribute.

```csharp
public class Person
{
    [MyCustomizedNpgsqlDbType(NpgsqlDbType.Text)]
    public string FirstName { get; set; }

    ...
}
```

Or even fluently map it into your entity.

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.FirstName, new MyCustomizedNpgsqlDbTypeAttribute(256));
```

Once mapped, all the operations will apply the settings to the corresponding property of the `IDbDataParameter` object _(for the targeted entity model property)_. The settings are also being applied even to the dynamic queries (like below).

```csharp
var people = connection.ExecuteQuery<Person>("[Person]", new { FirstName = "Somebody" });
```

### Other Data Providers

The support to the other data providers has also been introduced. Below are the pre-created attribute objects.

**[SQL Server](/attribute/sqlserver)**

- [CompareInfo](/attribute/sqlserver/compareinfo)
- [ForceColumnEncryption](/attribute/sqlserver/forcecolumnencryption)
- [LocaleId](/attribute/sqlserver/localeid)
- [Offset](/attribute/sqlserver/offset)
- [SqlDbType](/attribute/sqlserver/sqldbtype)
- [TypeName](/attribute/sqlserver/typename)
- [UdtTypeName](/attribute/sqlserver/udttypename)
- [XmlSchemaCollectionDatabase](/attribute/sqlserver/xmlschemacollectiondatabase)
- [XmlSchemaCollectionName](/attribute/sqlserver/xmlschemacollectionname)
- [XmlSchemaCollectionOwningSchema](/attribute/sqlserver/xmlschemacollectionowningschema)

**[MySQL](/attribute/mysql)**

- [MySqlDbType](/attribute/mysql/mysqldbtype)

**[SQLite](/attribute/sqlite)**

- [SqliteType](/attribute/sqlite/sqlitetype)
- [TypeName](/attribute/sqlite/typename)

**[PostgreSQL](/attribute/npgsql)**

- [ConvertedValue](/attribute/npgsql/convertedvalue)
- [DataTypeName](/attribute/npgsql/datatypename)
- [NpgsqlDbType](/attribute/npgsql/npgsqldbtype)

It is quitely simple, yet powerful. We find this capability very useful for those of you who are working on an advance cases in which the settings are impossible to be acquired via the existing operations.

For any issues found in relation to this capability, please [report](https://github.com/mikependon/RepoDB/issues/new?assignees=mikependon&labels=bug&template=report-a-bug.md&title=Bug%3A+%3CYour+bug%2Fissue+title%3E) it directly to our [Github](https://github.com/mikependon/RepoDB) page.