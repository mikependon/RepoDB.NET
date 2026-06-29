---
layout: default
sidebar: mappers
title: "FluentMapper"
description: "A class that is being used to define a mapping for the target data entity in a fluent way."
permalink: /mapper/fluentmapper
tags: [repodb, fluentmapper]
parent: MAPPERS
---

# FluentMapper

---

A mapper class for defining mappings for data entities and .NET CLR types. It enables implicit mapping configuration without requiring attribute decorations on classes.

### Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Entity | Gets the mapping definition for the specific data entity. |
| Type | Gets the mapping definition for the specific .NET CLR type. |

### Capabilities

- Maps the equivalent database object of the data entity.
- Maps the data entity primary and identity property.
- Maps the equivalent column of the property.
- Maps the class/property handlers of the property and .NET CLR type.
- Maps the parameter attributes of the property.

### Entity Mapping

To define mappings for a specific data entity type, use the `Entity()` method. It uses [EntityMapFluentDefinition](/class/entitymapfluentdefinition) to manage the mappings.

```csharp
FluentMapper
    .Entity<Customer>() // Define which Class or Model
    .Table("[sales].[Customer]") // Map the Class/Table
    .Primary(e => e.Id) // Define the Primary
    .Identity(e => e.Id) // Define the Identity
    .Column(e => e.FirstName, "[FName]") // Map the Property/Column
    .Column(e => e.LastName, "[LName]") // Map the Property/Column
    .Column(e => e.DateOfBirth, "[DOB]") // Map the Property/Column
    .DbType(e => e.DateOfBirth, DbType.DateTime2) // Defines the DatabaseType of the Property
    .ClassHandler<CustomerClassHandler>() // Defines the ClassHandler of the Model
    .PropertyHandler<CustomerAddressPropertyHandler>(e => e.Address)
    .PropertyValueAttributes<CustomerAddressPropertyHandler>(e => e.FirstName,
        new PropertyValueAttribute[]
        {
            new NameAttribute("[FName]),
            new SizeAttribute(256),
            new DbTypeAttribute(DbType.NVarChar)
        }); // Defines the PropertyValueAttribute(s) of the Property
```

### Type-Level Mapping

To define mappings for a specific .NET CLR type, use the `Type()` method. It uses [TypeMapFluentDefinition](/class/typemapfluentdefinition) to manage the mappings.

```csharp
FluentMapper
    .Type<DateTime>() // Define which .NET CLR type
    .DbType(DbType.DateTime2) // Defines the DatabaseType of the .NET CLR type
    .PropertyHandler<DateTimeToUtcKindTypeHandler>(); // Defines the PropertyHandler of the .NET CLR type
```

{: .note }
> Type-level mappings apply to all data entity properties of the same type.
