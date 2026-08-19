---
layout: default
title: SourceColumn
permalink: /attribute/mariadb/mariadbconnector/sourcecolumn
tags: [repodb, attribute, sourcecolumn, mariadbconnector]
parent: MariaDbConnector
grand_parent: MariaDB
---

# SourceColumn

---

This attribute sets the `MariaDbParameter.SourceColumn` property value via a class property.

> Works with the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector)-based [RepoDb.Connector.MariaDbConnector](https://www.nuget.org/packages/RepoDb.Connector.MariaDbConnector) package, via `RepoDb.MariaDbConnector`.

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

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.

{: .note }
> The `RepoDb.Connector.MariaDb` and `RepoDb.Connector.MariaDbConnector` packages each ship their own, separately-compiled `SourceColumnAttribute` type under the same namespace and simple name. They are not interchangeable — use the one that comes from whichever of [RepoDb.MariaDb](/attribute/mariadb/mariadb/sourcecolumn) or `RepoDb.MariaDbConnector` your project references.
