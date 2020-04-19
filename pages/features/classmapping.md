---
layout: navpage
sidebar: features
title: "Class Mapping"
permalink: /feature/classmapping
tags: [repodb, class, classmapping, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Class Mapping

This is the feature that would allow you to map the .NET CLR type (or class properties) into its equivalent database objects and types. It includes the `Class Name`, `Property Name`, `Primary`, `Identity` and `Database Type` mappings.

#### Class Name Mapping

To map the class name, simply use the [Map](/attribute/map) attribute.

```csharp
[Map("[sales].[Customer"])]
public class Customer
{
    ...
}
```

Or, use the [ClassMapper](/mapper/classmapper) class for attribute-free setup.

```csharp
ClassMapper.Add<Customer>("[sales].[Customer]");
```

#### Property Name Mapping

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

Or, use the [PropertyMapper](/mapper/propertymapper) class for attribute-free setup.

```csharp
PropertyMapper.Add<Customer>(e => e.FirstName, "[FName]");
PropertyMapper.Add<Customer>(e => e.LastName, "[LName]");
```

#### Primary Mapping

To map the class primary property, simply use the [Primary](/attribute/primary) attribute.

```csharp
public class Customer
{
    [Primary]
    public int Id { get; set; }

    ...
}
```

Or, use the [PrimaryMapper](/mapper/primarymapper) class for attribute-free setup.

```csharp
PropertyMapper.Add<Customer>(e => e.Id);
```

#### Identity Mapping

To map the class identity property, simply use the [Identity](/attribute/identity) attribute.

```csharp
public class Customer
{
    [Identity]
    public int Id { get; set; }

    ...
}
```

Or, use the [IdentityMapper](/mapper/identitymapper) class for attribute-free setup.

```csharp
IdentityMapper.Add<Customer>(e => e.Id);
```

#### Database Type Mapping

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

Or, use the [TypeMapper](/mapper/typemapper) class for attribute-free setup.

```csharp
TypeMapper.Add<Customer>(e => e.DateOfBirth, DbType.DateTime2);
```

###### Type Level

You can also use the [TypeMapper](/mapper/typemapper) class to map the class .NET CLR type into its equivalent database type mapping.

It is useful if you would like to always map the `System.DateTime` .NET CLR type equivalent to `DbType.DateTime2` database type.

```csharp
TypeMapper.Add<DateTime>(DbType.DateTime2);
```

Please visit the [Type Mapping](/feature/typemapping) feature for further information.