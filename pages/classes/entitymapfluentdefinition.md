---
layout: default
sidebar: classes
title: "EntityMapFluentDefinition"
description: "A class that is being used to define a data entity level mappings (i.e.: Table, Column, Primary, Identity, DB Type and Class/Property Handler)."
permalink: /class/entitymapfluentdefinition
tags: [repodb, entitymapfluentdefinition]
parent: CLASSES
---

# EntityMapFluentDefinition

---

A class used to define entity-level mappings for tables, columns, primary and identity keys, database types, and class/property handlers. It is returned by the `Entity()` method of the [FluentMapper](/mapper/fluentmapper) class.

## Mapping a Database Table/View

Use the `Table()` method to map an entity to a database table.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Table("[sales].[Customer]");
```

Uses [ClassMapper](/mapper/classmapper) internally.

## Mapping a Table Column

Use the `Column()` method to map an entity property to a table column.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Column(e => e.FirstName, "[FName]");
```

Or via property name.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Column("FirstName", "[FName]");
```

Or via [Field](/class/field) object.

```csharp
var definition = FluentMapper.Entity<Customer>();
var field = new Field("FirstName");
definition.Column(field, "[FName]");
```

Uses [PropertyMapper](/mapper/propertymapper) internally.

## Defining a Primary Property

Use the `Primary()` method to define the primary key property.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Primary(e => e.Id);
```

Or via property name.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Primary("Id");
```

Or via [Field](/class/field) object.

```csharp
var definition = FluentMapper.Entity<Customer>();
var field = new Field("Id");
definition.Primary(field);
```

Uses [PrimaryMapper](/mapper/primarymapper) internally.

## Defining an Identity Property

Use the `Identity()` method to define the identity property.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Identity(e => e.Id);
```

Or via property name.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Identity("Id");
```

Or via [Field](/class/field) object.

```csharp
var definition = FluentMapper.Entity<Customer>();
var field = new Field("Id");
definition.Identity(field);
```

Uses [IdentityMapper](/mapper/identitymapper) internally.

## Mapping a Database Type

Use the `DbType()` method to map a database type to an entity property.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.DbType(e => e.DateOfBirth, DbType.DateTime2);
```

Or via property name.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.DbType("DateOfBirth", DbType.DateTime2);
```

Or via [Field](/class/field) object.

```csharp
var definition = FluentMapper.Entity<Customer>();
var field = new Field("DateOfBirth");
definition.Identity(field, DbType.DateTime2);
```

Uses [TypeMapper](/mapper/typemapper) internally.

## Defining a Class Handler

Use the `ClassHandler` method to assign a class handler to the entity.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.ClassHandler<CustomerClassHandler>();
```

Uses [ClassHandlerMapper](/mapper/classhandlermapper) internally.

## Defining a Property Handler

Use the `PropertyHandler()` method to assign a property handler to an entity property.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.PropertyHandler<CustomerAddressPropertyHandler>(e => e.Address);
```

Or via property name.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.PropertyHandler<CustomerAddressPropertyHandler>("Address");
```

Or via [Field](/class/field) object.

```csharp
var definition = FluentMapper.Entity<Customer>();
var field = new Field("Address");
definition.PropertyHandler<CustomerAddressPropertyHandler>("Address");
```

Uses [PropertyHandlerMapper](/mapper/propertyhandlermapper) internally.
