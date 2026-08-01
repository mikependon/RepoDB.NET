---
layout: default
title: MySqlDbType
permalink: /attribute/mysql/mysqlconnector/mysqldbtype
tags: [repodb, attribute, mysqldbtype, mysqlconnector]
parent: MySqlConnector
grand_parent: MySQL
---

# MySqlDbType

---

This attribute sets the `MySqlParameter.MySqlDbType` property value via a class property.

> Works with the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector) package, via `RepoDb.MySqlConnector`.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [MySqlDbType(MySqlDbType.Text)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new MySqlDbTypeAttribute(MySqlDbType.Text))
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(MySqlDbTypeAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(MySqlDbTypeAttribute));
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.
