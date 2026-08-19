---
layout: default
title: MariaDbType
permalink: /attribute/mariadb/mariadbconnector/mariadbtype
tags: [repodb, attribute, mariadbtype, mariadbconnector]
parent: MariaDbConnector
grand_parent: MariaDB
---

# MariaDbType

---

This attribute sets the `MariaDbParameter.MariaDbType` property value via a class property.

> Works with the [MySqlConnector](https://www.nuget.org/packages/MySqlConnector)-based [RepoDb.Connector.MariaDbConnector](https://www.nuget.org/packages/RepoDb.Connector.MariaDbConnector) package, via `RepoDb.MariaDbConnector`.

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
> The `RepoDb.Connector.MariaDb` and `RepoDb.Connector.MariaDbConnector` packages each ship their own, separately-compiled `MariaDbTypeAttribute` type under the same namespace and simple name. They are not interchangeable — use the one that comes from whichever of [RepoDb.MariaDb](/attribute/mariadb/mariadb/mariadbtype) or `RepoDb.MariaDbConnector` your project references.
