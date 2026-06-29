---
layout: default
title: XmlSchemaCollectionOwningSchema
permalink: /attribute/sqlserver/xmlschemacollectionowningschema
tags: [repodb, attribute, xmlschemacollectionowningschema]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# XmlSchemaCollectionOwningSchema

---

This attribute sets the `SqlParameter.XmlSchemaCollectionOwningSchema` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [XmlSchemaCollectionName("XmlSchemaCollectionOwningSchema")]
    public string XmlSchemaCollectionOwningSchema { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new XmlSchemaCollectionOwningSchema("XmlSchemaCollectionOwningSchema"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionOwningSchema));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(XmlSchemaCollectionOwningSchema));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
