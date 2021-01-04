---
layout: default
sidebar: mappers
title: "FluentMapper"
description: "A class that is used to define a mapping for the target data entity in a fluent way."
permalink: /mapper/fluentmapper
tags: [repodb, class, fluentmapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: MAPPERS
---

# FluentMapper

---

A mapper classed that is used to define the mappings for both data entity and .NET CLR type. This class has been introduced to help the developers manage the mappings implicitly (by avoiding decorating the classes with different attributes).

### Methods

Below are the methods available from this class.

- `Entity` - gets the mapping definition for the specific data entity.
- `Type` - get the mapping definition for the specific .NET CLR type.

#### By using this class, the developers can do the following.

- Maps the equivalent `database object` of the data entity.
- Define the data entity `primary` and `identity` property.
- Maps the equivalent `column` of the property.
- Defines the database type and `class/property handler` of the property and .NET CLR type.

### Entity Mapping

To define the mappings for specific data entity type, use the `Entity()` method. It uses the [EntityMapFluentDefinition](/class/entitymapfluentdefinition) class to manage the mappings.

You can create an instance of definition like below.

```csharp
var customerMappingDefinition = FluentMapper.Entity<Customer>(); // Define which Class or Model
```

And call the necessary methods like below.

```csharp
customerMappingDefinition
    .Table("[sales].[Customer]") // Map the Class/Table
    .Primary(e => e.Id) // Define the Primary
    .Identity(e => e.Id) // Define the Identity
    .Column(e => e.FirstName, "[FName]") // Map the Property/Column
    .Column(e => e.LastName, "[LName]") // Map the Property/Column
    .Column(e => e.DateOfBirth, "[DOB]") // Map the Property/Column
    .DbType(e => e.DateOfBirth, DbType.DateTime2) // Defines the DatabaseType of the Property
    .ClassHandler<CustomerClassHandler>() // Defines the ClassHandler of the Model
    .PropertyHandler<CustomerAddressPropertyHandler>(e => e.Address); // Defines the PropertyHandler of the Property
```

Or, if you are not maintaining the definition instance, then you can directly call the method like below.

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
    .PropertyHandler<CustomerAddressPropertyHandler>(e => e.Address); // Defines the PropertyHandler of the Property
```

### Type-Level Mapping

To define the mappings for a specific .NET CLR type, use the `Type()` method. It uses the [TypeMapFluentDefinition](/class/typemapfluentdefinition) class to manage the mappings.

You can create an instance of definition like below.

```csharp
var dateTimeMappingDefinition = FluentMapper.Type<DateTime>(); // Define which .NET CLR type
```

And call the necessary methods like below.

```csharp
dateTimeMappingDefinition
    .DbType(DbType.DateTime2) // Defines the DatabaseType of the .NET CLR type
    .PropertyHandler<DateTimeToUtcKindTypeHandler>(); // Defines the PropertyHandler of the .NET CLR type
```

Or, if you are not maintaining the definition instance, then you can directly call the method like below.

```csharp
FluentMapper
    .Type<DateTime>() // Define which .NET CLR type
    .DbType(DbType.DateTime2) // Defines the DatabaseType of the .NET CLR type
    .PropertyHandler<DateTimeToUtcKindTypeHandler>(); // Defines the PropertyHandler of the .NET CLR type
```

> By using the type-level mapping, all data entity properties with the same type will be affected.
