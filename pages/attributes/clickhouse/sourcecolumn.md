---
layout: default
title: SourceColumn
permalink: /attribute/clickhouse/sourcecolumn
tags: [repodb, attribute, sourcecolumn, clickhouse]
parent: ClickHouse
grand_parent: ATTRIBUTES
---

# SourceColumn

---

This attribute sets the `ClickHouseDbParameter.SourceColumn` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public ulong Id { get; set; }

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
