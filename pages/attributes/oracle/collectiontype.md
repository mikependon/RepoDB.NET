---
layout: default
title: CollectionType
permalink: /attribute/oracle/collectiontype
tags: [repodb, attribute, collectiontype]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# CollectionType

---

This attribute sets the `OracleParameter.CollectionType` property value via a class property. Set this to `OracleCollectionType.PLSQLAssociativeArray` together with [ArrayBindSize](/attribute/oracle/arraybindsize) when binding a PL/SQL associative array (e.g. for array-bind based batch execution).

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [CollectionType(OracleCollectionType.PLSQLAssociativeArray)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new CollectionTypeAttribute(OracleCollectionType.PLSQLAssociativeArray));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(CollectionTypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
