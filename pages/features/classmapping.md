---
layout: default
sidebar: features
title: "Class Mapping"
description: "This is a feature that would allow you to map the .NET CLR type (or class properties) into its equivalent database objects and types."
permalink: /feature/classmapping
tags: [repodb, classmapping]
parent: FEATURES
---

# Class Mapping

---

This feature allows you to map .NET CLR types or class properties to their equivalent database objects and types. It covers class and property name mapping, primary and identity columns, class and property handlers, property attributes, and database types.

## Class Name Mapping

Use the [Map](/attribute/map) attribute to map the class name.

```csharp
[Map("[sales].[Customer]")]
public class Customer
{
    ...
}
```

Or use the [Table](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.schema.tableattribute?view=net-6.0) attribute from the [System.ComponentModel.DataAnnotations.Schema](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.schema?view=net-6.0) namespace.

```csharp
[Table("[sales].[Customer]")]
public class Customer
{
    ...
}
```

Or use the [FluentMapper](/mapper/fluentmapper) class for an attribute-free setup. It uses [ClassMapper](/mapper/classmapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Table("[sales].[Customer]")
```

## Property Name Mapping

Use the [Map](/attribute/map) attribute to map a property name.

```csharp
public class Customer
{
    public int Id { get; set; }

    [Map("FName")]
    public string FirstName { get; set;}

    [Map("LName")]
    public string LastName { get; set;}

    ...
}
```

Or use the `Column` attribute from the [System.ComponentModel.DataAnnotations.Schema](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.schema?view=net-6.0) namespace.

```csharp
public class Customer
{
    public int Id { get; set; }

    [Column("FName")]
    public string FirstName { get; set;}

    [Column("LName")]
    public string LastName { get; set;}

    ...
}
```

Or use the [FluentMapper](/mapper/fluentmapper) class for an attribute-free setup. It uses [PropertyMapper](/mapper/propertymapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Column(e => e.FirstName, "[FName]")
    .Column(e => e.LastName, "[LName]");
```

## Primary Mapping

Use the [Primary](/attribute/primary) attribute to designate a primary property.

```csharp
public class Customer
{
    [Primary]
    public int Id { get; set; }

    ...
}
```

Or use the `Key` attribute from the `System.ComponentModel.DataAnnotations` namespace.

```csharp
public class Customer
{
    [Key]
    public int Id { get; set; }

    ...
}
```

Or use the [FluentMapper](/mapper/fluentmapper) class for an attribute-free setup. It uses [PrimaryMapper](/mapper/primarymapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Primary(e => e.Id);
```

## Identity Mapping

Use the [Identity](/attribute/identity) attribute to designate an identity property.

```csharp
public class Customer
{
    [Identity]
    public int Id { get; set; }

    ...
}
```

Or use the [FluentMapper](/mapper/fluentmapper) class for an attribute-free setup. It uses [IdentityMapper](/mapper/identitymapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Identity(e => e.Id);
```

## Class Handler Mapping

Use the [ClassHandler](/attribute/classhandler) attribute to map a class handler.

```csharp
[ClassHandler(CustomerClassHandler)]
public class Customer
{
    public int Id { get; set; }
    ...
}
```

Or use the [FluentMapper](/mapper/fluentmapper) class for an attribute-free setup. It uses [ClassHandlerMapper](/mapper/classhandlermapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .ClassHandler<CustomerClassHandler>();
```

## Property Handler Mapping

Use the [PropertyHandler](/attribute/propertyhandler) attribute to map a property handler to a class property.

```csharp
public class Customer
{
    public int Id { get; set; }

    ...

    [PropertyHandler(PersonAddressPropertyHandler)]
    public Address Address { get; set; }

    ...
}
```

Or use the [FluentMapper](/mapper/fluentmapper) class for an attribute-free setup. It uses [PropertyHandlerMapper](/mapper/propertyhandlermapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .PropertyHandler<PersonAddressPropertyHandler>(e => e.Address);
```

## Database Type Mapping

Use the [TypeMap](/attribute/typemap) attribute to map a class property to a database type.

```csharp
public class Customer
{
    public int Id { get; set; }

    ...

    [TypeMap(DbType.DateTime2)]
    public DateTime DateOfBirth { get; set; }

    ...
}
```

Or use the [FluentMapper](/mapper/fluentmapper) class for an attribute-free setup. It uses [TypeMapper](/mapper/typemapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Column(e => e.DateOfBirth, DbType.DateTime2);
```

#### Type Level

Use [FluentMapper](/mapper/fluentmapper) or [TypeMapper](/mapper/typemapper) to map a .NET CLR type to its equivalent database type.

```csharp
FluentMapper
    .Type<DateTime>()
    .DbType(DbType.DateTime2);
```

Please visit the [Type Mapping](/feature/typemapping) feature for further information.
