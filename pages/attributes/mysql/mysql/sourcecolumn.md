---
layout: default
title: SourceColumn
permalink: /attribute/mysql/mysql/sourcecolumn
tags: [repodb, attribute, sourcecolumn, mysql]
parent: MySql
grand_parent: MySQL
---

# SourceColumn

---

This attribute sets the `MySqlParameter.SourceColumn` property value via a class property.

> Works with the [MySql.Data](https://www.nuget.org/packages/MySql.Data) package, via `RepoDb.MySql`.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SourceColumn("Name")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SourceColumnAttribute("Name"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceColumnAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceColumnAttribute));
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.
