---
layout: default
title: SourceColumnNullMapping
permalink: /attribute/mysql/mysqlconnector/sourcecolumnnullmapping
tags: [repodb, attribute, sourcecolumnnullmapping, mysqlconnector]
parent: MySqlConnector
grand_parent: MySQL
---

# SourceColumnNullMapping

---

This attribute sets the `MySqlParameter.SourceColumnNullMapping` property value via a class property.

> Works with the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector) package, via `RepoDb.MySqlConnector`.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SourceColumnNullMapping(true)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SourceColumnNullMappingAttribute(true));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceColumnNullMappingAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceColumnNullMappingAttribute));
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.
