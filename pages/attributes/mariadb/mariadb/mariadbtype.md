---
layout: default
title: MariaDbType
permalink: /attribute/mariadb/mariadb/mariadbtype
tags: [repodb, attribute, mariadbtype, mariadb]
parent: MariaDb
grand_parent: MariaDB
---

# MariaDbType

---

This attribute sets the `MariaDbParameter.MariaDbType` property value via a class property.

> Works with the [MySql.Data](https://www.nuget.org/packages/MySql.Data)-based [RepoDb.Connector.MariaDb](https://www.nuget.org/packages/RepoDb.Connector.MariaDb) package, via `RepoDb.MariaDb`.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [MariaDbType(MariaDbType.Text)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new MariaDbTypeAttribute(MariaDbType.Text));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(MariaDbTypeAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(MariaDbTypeAttribute));
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.

{: .note }
> The `RepoDb.Connector.MariaDb` and `RepoDb.Connector.MariaDbConnector` packages each ship their own, separately-compiled `MariaDbTypeAttribute` type under the same namespace and simple name. They are not interchangeable — use the one that comes from whichever of `RepoDb.MariaDb` or [RepoDb.MariaDbConnector](/attribute/mariadb/mariadbconnector/mariadbtype) your project references.
