---
layout: post
title: "PropetyValueAttribute - A Dynamic Settings for IDbDataParameter"
author: "Michael Camara Pendon"
date: 2021-09-28 13:00:00 +0200
categories: blogs repodb
---

### TL;DR

We are happy to announce the availability of the [PropertyValueAttribute](/attribute/propertyvalue) attribute, a powerful object that would allow you (as a user of the library) to control the property value setting on the `IDbDataParameter` object before the actual execution against the database.

### Implicit Mapping

Currenly, RepoDB allows you to fluently map a property into its destination object from the database (i.e.: `TableName/ViewName` and `ColumnName`).

> The main goal is to eliminate the decoration of the attributes on your entity model and property, and also, ensuring your model to be decoupled from the ORM.

So you do not need to do this.

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

However, such support is limited only to the following objects.

- TableName/ViewName
- ColumnName
- DbType
- Identity/Primary
- ClassHandler/PropertyHandler

### Explicit Attribute

Before the [RepoDb v1.12.9](https://www.nuget.org/packages/RepoDb/1.12.9) release, there is no way we can override the value being passed to the `IDbDataParameter` object when the operation is being executed against the database.

To be more specific, in the sample below, there is no way we can explicitly set the value of the `Size` property of the `IDbDataParameter` object that is connected to the `FirstName` property of the `Person` entity.

```csharp
[Map("[dbo].[Person]")]
public class Person
{
    [Map("FName")]
    public string FirstName { get; set; }

    ...
}
```

As part of the [RepoDb v1.12.9](https://www.nuget.org/packages/RepoDb/1.12.9) release, we had introduced the [SizeAttribute](/attribute/parameter/size) object, which inheritted the [PropertyValueAttribute](/attribute/parameter/propertyvalueattribute) object. Thgough this, we can explicitly override the value being set on the `Size` property of the `IDbDataParameter` object.

```csharp
[Map("[dbo].[Person]")]
public class Person
{
    [Map("FName"), Size(256)]
    public string FirstName { get; set; }

    ...
}
```

The equivalent fluent mapping is below.

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.FirstName, new SizeAttribute(256));
```

> The other attributes (i.e.: [DbType](/attribute/parameter/dbtype), [Direction](/attribute/parameter/direction), [IsNullable](/attribute/parameter/isnullable), [Name](/attribute/parameter/name), [Precision](/attribute/parameter/precision), [Scale](/attribute/parameter/scale), [Size](/attribute/parameter/size)) has also been introduced.

We can also map multiple attributes together (in one go).

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

### PropertyValueAttribute

_What is really more exciting is the dynamic support_ to the [PropertyValueAttribute](/attribute/parameter/propertyvalueattribute) object.  This object is capable of extending all the scenarios, for as long the intention is to set the property value of the `IDbDataParameter` object.

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
[Map("[dbo].[Person]")]
public class Person
{
    [Map("FName"), MyCustomizedNpgsqlDbType(NpgsqlDbType.Text)]
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

The constructor only accepts 3 arguments. It requires to be called on the derived class.

- ParameterType - the type of the `IDbDataParameter` object.
- PropertyName - the name of the target property from the `IDbDataParameter` object.
- Value - the actual value to be set.

### Other Data Providers

Lastly, the support to the other data providers has also been introduced. Below are the pre-created objects.

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

**[PostgreSQL](/attribute/npgsql)**

- [ConvertedValue](/attribute/npgsql/convertedvalue)
- [DataTypeName](/attribute/npgsql/datatypename)
- [NpgsqlDbType](/attribute/npgsql/npgsqldbtype)

**[MySQL](/attribute/mysql)**

- [MySqlDbType](/attribute/mysql/mysqldbtype)

**[SQLite](/attribute/sqlite)**

- [SqliteType](/attribute/sqlite/sqlitetype) - Microsoft
- [TypeName](/attribute/sqlite/typename) - System


Thank you for reading and also to all our contributors and collaborators. For any issues found, please report it directly to our [Github](https://github.com/mikependon/RepoDB/issues/new?assignees=mikependon&labels=bug&template=report-a-bug.md&title=Bug%3A+%3CYour+bug%2Fissue+title%3E) page.