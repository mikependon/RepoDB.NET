---
layout: default
title: PropertyValueAttribute
nav_order: 1
permalink: /attribute/parameter/propertyvalueattribute
tags: [repodb, attribute, propertyvalueattribute]
parent: Parameter
grand_parent: ATTRIBUTES
---

# PropertyValueAttribute

---

The base class for all attributes that set properties on a [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) object.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [PropertyValue(typeof(DbParameter), nameof(DbParameter.ParameterName), "CompleteName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name,
        new PropertyValueAttribute(typeof(DbParameter), nameof(DbParameter.ParameterName), "CompleteName"));
```

Multiple attributes can be mapped at once:

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new []
    {
        new PropertyValueAttribute(typeof(DbParameter), nameof(DbParameter.ParameterName), "CompleteName")
        new PropertyValueAttribute(typeof(DbParameter), nameof(DbParameter.DbType), DbType.NVarChar)
    });
```

### Retrieval

Retrieve the list of mapped attributes via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attributes = PropertyValueAttributeCache.Get<Person>(e => e.Name);
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attributes = PropertyValueAttributeMapper.Get<Person>(e => e.Name);
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.

### Implementation

To create a custom attribute, inherit this class:

```csharp
public class CustomizedNameAttribute : PropertyValueAttribute
{
    public CustomizedNameAttribute(string parameterName)
        : base(typeof(DbParameter), nameof(DbParameter.ParameterName), parameterName)
    { }
}
```

The custom attribute can then be applied to a class:

```csharp
public class Person
{
    public int Id { get; set; }

    [CustomizedName("CompleteName")]
    public string Name { get; set; }
}
```

Or via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name,
        new [] { new CustomizedNameAttribute("CompleteName" })
```

{: .note }
> The implementation is dynamic — any property of `IDbDataParameter` can be targeted, regardless of the data provider (SQL Server, PostgreSQL, MySQL, or SQLite).
