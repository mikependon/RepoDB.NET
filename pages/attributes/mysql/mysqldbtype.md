---
layout: default
title: MySqlDbType
permalink: /attribute/mysql/mysqldbtype
tags: [repodb, attribute, mysqldbtype]
parent: MySQL
grand_parent: ATTRIBUTES
---

# MySqlDbType

---

This attribute sets the `MySqlParameter.MySqlDbType` property value via a class property.

> Compatible with both the `MySql.Data` and `MySqlConnector` packages.

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