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

A class that is being used to define a data entity level mappings specifically for the table/columns, primary/identity columns, database types and class/property handlers. It is the result of the `Entity()` method of the [FluentMapper](/mapper/fluentmapper) mapper class.

## Mapping a Database Table/View

To map a data entity into a specific database table, use the `Table()` method.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Table("[sales].[Customer]");
```

It is using the [ClassMapper](/mapper/classmapper) class underneath.

## Mapping a Table Column

To map a data entity property into a specific table column, use the `Column()` method.

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

It is using the [PropertyMapper](/mapper/propertymapper) class underneath.

## Defining a Primary Property

To define the data entity primary property, use the `Primary()` method.

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

It is using the [PrimaryMapper](/mapper/primarymapper) class underneath.

## Defining an Identity Property

To define the data entity identity property, use the `Identity()` method.

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

It is using the [IdentityMapper](/mapper/identitymapper) class underneath.

## Mapping a Database Type

To map an equivalent database type into a data entity property, use the `DbType()` method.

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

It is using the [TypeMapper](/mapper/typemapper) class underneath.

## Defining a Class Handler

To define the class handler for the data entity, use the `ClassHandler` method.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.ClassHandler<CustomerClassHandler>();
```

It is using the [ClassHandlerMapper](/mapper/classhandlermapper) class underneath.

## Defining a Property Handler

To define the property handler for the data entity property, use the `PropertyHandler()` method.

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

It is using the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class underneath.

