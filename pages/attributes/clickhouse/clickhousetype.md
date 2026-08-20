---
layout: default
title: ClickHouseType
permalink: /attribute/clickhouse/clickhousetype
tags: [repodb, attribute, clickhousetype, clickhouse]
parent: ClickHouse
grand_parent: ATTRIBUTES
---

# ClickHouseType

---

This attribute sets the `ClickHouseDbParameter.ClickHouseType` property value via a class property, forcing an explicit ClickHouse type name for the generated parameter.

### Attribute

Example usage:

```csharp
public class Person
{
    public ulong Id { get; set; }

    [ClickHouseType("Nullable(DateTime64(3))")]
    public DateTime? CreatedDateUtc { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.CreatedDateUtc, new ClickHouseTypeAttribute("Nullable(DateTime64(3))"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.CreatedDateUtc)?
    .FirstOrDefault(e => e.GetType() == typeof(ClickHouseTypeAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.CreatedDateUtc)?
    .FirstOrDefault(e => e.GetType() == typeof(ClickHouseTypeAttribute));
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.

{: .note }
> The underlying driver infers a plain `DateTime` CLR parameter as whole-second precision. Writing sub-second values into a `DateTime64(N)` column silently truncates them unless the parameter's ClickHouse type is stated explicitly, as in the example above.
