---
layout: default
title: SqliteType
permalink: /attribute/sqlite/sqlitetype
tags: [repodb, attribute, sqlitetype]
parent: SQLite
grand_parent: ATTRIBUTES
nav_exclude: true
---

# SqliteType

---

This attribute sets the `SqliteParameter.SqliteType` property value via a class property.

> Only works with the `Microsoft.Data.Sqlite` package.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SqliteType(SqliteType.Text)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SqliteTypeAttribute(SqliteType.Text));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SqliteTypeAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SqliteTypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
