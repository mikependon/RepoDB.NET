---
layout: default
title: SourceVersion
permalink: /attribute/mysql/mysql/sourceversion
tags: [repodb, attribute, sourceversion, mysql]
parent: MySql
grand_parent: MySQL
---

# SourceVersion

---

This attribute sets the `MySqlParameter.SourceVersion` property value via a class property.

> Works with the [MySql.Data](https://www.nuget.org/packages/MySql.Data) package, via `RepoDb.MySql`.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SourceVersion(DataRowVersion.Current)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SourceVersionAttribute(DataRowVersion.Current));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceVersionAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceVersionAttribute));
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.
