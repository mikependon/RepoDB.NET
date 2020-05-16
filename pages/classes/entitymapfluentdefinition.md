---
layout: navpage
sidebar: classes
title: "EntityMapFluentDefinition"
description: "A class that is used to define a data entity level mappings (ie: Table, Column, Primary, Identity, DB Type and Property Handler)."
permalink: /class/entitymapfluentdefinition
tags: [repodb, class, entitymapfluentdefinition, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# EntityMapFluentDefinition

A class that is used to define a data entity level mappings (ie: `Table`, `Column`, `Primary`, `Identity`, `DB Type` and `Property Handler`). It is the result of the `Entity()` method of the [FluentMapper](/mapper/fluentmapper) mapper class.

#### Mapping a Database Table/View

To map a data entity into a specific database table, use the `Table` method.

```csharp
var definition = FluentMapper.Entity<Customer>();
definition.Table("[sales].[Customer]");
```

#### Mapping a Table Column

To map a data entity property into a specific table column, use the `Column` method.

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

#### Defining a Primary Property

To define the data entity primary property, use the `Primary` method.

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

#### Defining an Identity Property

To define the data entity identity property, use the `Identity` method.

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

#### Mapping a Database Type

To map an equivalent database type into a data entity property, use the `DbType` method.

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

#### Defining a Property Handler

To define the property handler for the data entity property, use the `PropertyHandler` method.

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



