---
layout: default
sidebar: features
title: "Class Mapping"
description: "This is a feature that would allow you to map the .NET CLR type (or class properties) into its equivalent database objects and types."
permalink: /feature/classmapping
tags: [repodb, class, classmapping, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: FEATURES
---

# Class Mapping

---

This is a feature that would allow you to map any .NET CLR types or class properties into its equivalent database objects and types. It includes the mapping capabilities for the class/property name, primary/identity columns, class/property handlers and the database types.

### Class Name Mapping

To map the class name, simply use the [Map](/attribute/map) attribute.

```csharp
[Map("[sales].[Customer]")]
public class Customer
{
    ...
}
```

Or, use the `Table` attribute of `System.ComponentModel.DataAnnotations.Schema` namespace.

```csharp
[Table("[sales].[Customer]")]
public class Customer
{
    ...
}
```

Or, use the [FluentMapper](/mapper/fluentmapper) class for attribute-free setup. It uses the [ClassMapper](/mapper/classmapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Table("[sales].[Customer]")
```

### Property Name Mapping

To map the property name, simply use the [Map](/attribute/map) attribute.

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

Or, use the the `Column` attribute of `System.ComponentModel.DataAnnotations.Schema` namespace.

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

Or, use the [FluentMapper](/mapper/fluentmapper) class for attribute-free setup. It uses the [PropertyMapper](/mapper/propertymapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Column(e => e.FirstName, "[FName]")
    .Column(e => e.LastName, "[LName]");
```

### Primary Mapping

To map the class primary property, simply use the [Primary](/attribute/primary) attribute.

```csharp
public class Customer
{
    [Primary]
    public int Id { get; set; }

    ...
}
```

Or, use the `Key` attribute of `System.ComponentModel.DataAnnotations` namespace.

```csharp
public class Customer
{
    [Key]
    public int Id { get; set; }

    ...
}
```

Or, use the [FluentMapper](/mapper/fluentmapper) class for attribute-free setup. It uses the [PrimaryMapper](/mapper/primarymapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Primary(e => e.Id);
```

### Identity Mapping

To map the class identity property, simply use the [Identity](/attribute/identity) attribute.

```csharp
public class Customer
{
    [Identity]
    public int Id { get; set; }

    ...
}
```

Or, use the [FluentMapper](/mapper/fluentmapper) class for attribute-free setup. It uses the [IdentityMapper](/mapper/identitymapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Identity(e => e.Id);
```

### Class Handler Mapping

To map the class handler, simply use the [ClassHandler](/attribute/classhandler) attribute.

```csharp
[ClassHandler(CustomerClassHandler)]
public class Customer
{
    public int Id { get; set; }
    ...
}
```

Or, use the [FluentMapper](/mapper/fluentmapper) class for attribute-free setup. It uses the [ClassHandlerMapper](/mapper/classhandlermapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .ClassHandler<CustomerClassHandler>();
```

### Property Handler Mapping

To map the class property equivalent property handler, simply use the [PropertyHandler](/attribute/propertyhandler) attribute.

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

Or, use the [FluentMapper](/mapper/fluentmapper) class for attribute-free setup. It uses the [PropertyHandlerMapper](/mapper/propertyhandlermapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .PropertyHandler<PersonAddressPropertyHandler>(e => e.Address);
```

### Database Type Mapping

To map the class property equivalent database type, simply use the [TypeMap](/attribute/typemap) attribute.

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

Or, use the [FluentMapper](/mapper/fluentmapper) class for attribute-free setup. It uses the [TypeMapper](/mapper/typemapper) underneath.

```csharp
FluentMapper
    .Entity<Customer>()
    .Column(e => e.DateOfBirth, DbType.DateTime2);
```

#### Type Level

You can also use the [FluentMapper](/mapper/fluentmapper) or the [TypeMapper](/mapper/typemapper) classes to map the .NET CLR type into its equivalent database type mapping.

It is useful if you would like to always map the `System.DateTime` .NET CLR type equivalent to `DbType.DateTime2` database type.

```csharp
FluentMapper
    .Type<DateTime>()
    .DbType(DbType.DateTime2);
```

Please visit the [Type Mapping](/feature/typemapping) feature for further information.
