---
layout: default
title: SourceVersion
permalink: /attribute/mariadb/mariadb/sourceversion
tags: [repodb, attribute, sourceversion, mariadb]
parent: MariaDb
grand_parent: MariaDB
---

# SourceVersion

---

This attribute sets the `MariaDbParameter.SourceVersion` property value via a class property.

> Works with the [MySql.Data](https://www.nuget.org/packages/MySql.Data)-based [RepoDb.Connector.MariaDb](https://www.nuget.org/packages/RepoDb.Connector.MariaDb) package, via `RepoDb.MariaDb`.

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

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.

{: .note }
> The `RepoDb.Connector.MariaDb` and `RepoDb.Connector.MariaDbConnector` packages each ship their own, separately-compiled `SourceVersionAttribute` type under the same namespace and simple name. They are not interchangeable — use the one that comes from whichever of `RepoDb.MariaDb` or [RepoDb.MariaDbConnector](/attribute/mariadb/mariadbconnector/sourceversion) your project references.
