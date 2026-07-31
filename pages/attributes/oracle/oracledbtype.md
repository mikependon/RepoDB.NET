---
layout: default
title: OracleDbType
permalink: /attribute/oracle/oracledbtype
tags: [repodb, attribute, oracledbtype]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# OracleDbType

---

This attribute sets the `OracleParameter.OracleDbType` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [OracleDbType(OracleDbType.NVarchar2)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new OracleDbTypeAttribute(OracleDbType.NVarchar2));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(OracleDbTypeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance. See also [OracleDbTypeEx](/attribute/oracle/oracledbtypeex).
