---
layout: default
sidebar: features
title: "Implicit Mapping"
description: "This is a feature that would allow you to implicitly map the .NET CLR objects or class properties into its equivalent object in the database. Certain mapper classes has been provided in order to avoid the usage of the attributes in the classes."
permalink: /feature/implicitmapping
tags: [repodb, implicitmapping]
parent: FEATURES
---

# Implicit Mapping

---

This is a feature that would allow you to implicitly map the .NET CLR types or class properties into its equivalent object in the database. Certain mapper classes has been provided in order to avoid the usage of the attributes within the classes.

## Fluent Mapping

Use the [FluentMapper](/mapper/fluentmapper) class to fluently manage the mappings of the table/properties, primary/identity columns, database types and class/property handlers.

#### Property Level

To define the mappings for an entity, use the `Entity()` method.

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
    .ClassHandler<CustomerClassHandler>() // Defines the ClassHandler of the Class
    .PropertyHandler<CustomerAddressPropertyHandler>(e => e.Address); // Defines the PropertyHandler of the Property
    .PropertyValueAttributes<CustomerAddressPropertyHandler>(e => e.Address, new PropertyValueAttribute[]
    {
        new NameAttribute("Address"),
        new SizeAttribute(1024),
        new DbTypeAttribute(DbType.NVarChar)
    }); // Defines the PropertyHandler of the Property
```

#### Type Level

To define the mappings for a specfic .NET CLR type, use the `Type()` method.

```csharp
FluentMapper
    .Type<DateTime>() // Define which .NET CLR type
    .DbType(DbType.DateTime2) // Define the DatabaseType of the .NET CLR type
    .PropertyHandler<DateTimeKindToUtcPropertyHandler>(); // Define the PropertyHandler of the .NET CLR type

FluentMapper
    .Type<Microsoft.SqlServer.Types.SqlGeography>()
    .PropertyValueAttributes(new[]
    {
        new SqlDbTypeAttribute(SqlDbType.Udt),
        new UdtTypeNameAttribute("Geography")
    });
```

The priority of the mapping is first identified via an attribute-level, then followed by a property-level and lastly by a type-level.

> The [FluentMapper](/mapper/fluentmapper) is using the following classes ([ClassMapper](/mapper/classmapper), [ClassHandlerMapper](/mapper/classhandlermapper), [IdentityMapper](/mapper/identitymapper), [PrimaryMapper](/mapper/primarymapper), [PropertyHandlerMapper](/mapper/propertyhandlermapper), [PropertyMapper](/mapper/propertymapper) and [TypeMapper](/mapper/typemapper)) underneath to establish the proper mappings.

## Class Name Mapping

Use the [ClassMapper](/mapper/classmapper) class to manage the mappings of the class into its equivalent object in the database (i.e.: Table, View).

Let us say you have a `Customer` class which has an equivalent table named `Customer` under `sales` schema.

```csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set;}
    public string LastName { get; set;}
    ...
}
```

Then, use the `Add()` method to map it.

```csharp
ClassMapper.Add<Customer>("[sales].[Customer]");
```
To retrieve the mapped name, use the `Get()` method.

```csharp
var mappedName = ClassMapper.Get<Customer>();
```

> We highly recommend to use the [ClassMappedNameCache](/cacher/classmappednamecache) class when retrieving the mapped name. This is to maximize the reusability and performance.

```csharp
var mappedName = ClassMappedNameCache.Get<Customer>();
```

To remove the mapping, use the `Remove()` method.

```csharp
ClassMapper.Remove<Customer>();
```

## Identity Mapping

Use the [IdentityMapper](/mapper/identitymapper) class to manage the mappings of the class property as an identity.

To add a mapping, use the `Add()` method.

```csharp
IdentityMapper.Add<Customer>(e => e.Id);
```

To retrieve the mapping, use the `Get()` method. The method returns an instance of [ClassProperty](/class/classproperty) object.

```csharp
var property = IdentityMapper.Get<Customer>();
```

> We highly recommend to use the [IdentityCache](/cacher/identitycache) class when retrieving the identity property. This is to maximize the reusability and performance.

```csharp
var property = IdentityCache.Get<Customer>();
```

To remove the mapping, use the `Remove()` method.

```csharp
IdentityMapper.Remove<Customer>();
```

## Primary Mapping

Use the [PrimaryMapper](/mapper/primarymapper) class to manage the mappings of the class property as a primary.

To add a mapping, use the `Add()` method.

```csharp
PrimaryMapper.Add<Customer>(e => e.Id);
```
To retrieve the mapping, use the `Get()` method. The method returns an instance of [ClassProperty](/class/classproperty) object.

```csharp
var property = PrimaryMapper.Get<Customer>();
```

> We highly recommend to use the [PrimaryCache](/cacher/primarycache) class when retrieving the primary property. This is to maximize the reusability and performance.

```csharp
var mappedName = PrimaryCache.Get<Customer>();
```

To remove the mapping, use the `Remove()` method.

```csharp
PrimaryMapper.Remove<Customer>();
```

## ClassHandler Mapping

Use the [ClassHandlerMapper](/mapper/classhandlermapper) class to manage the mappings of the .NET CLR type class handler.

Let us say you have class handler below.

```csharp
public class CustomerClassHandler : IClassHandler<Customer>
{
    public Customer Get(Customer entity, DbDataReader reader)
    {
        return entity;
    }

    public Customer Set(Customer entity)
    {
        return entity;
    }
}
```

To add a mapping, use the `Add()` method.

```csharp
ClassHandlerMapper.Add<Customer, CustomerClassHandler>();
```

To retrieve the mapping, use the `Get()` method.

```csharp
var classHandler = ClassHandlerMapper.Get<Customer, CustomerClassHandler>();
```

> We highly recommend to use the [ClassHandlerCache](/cacher/classhandlercache) class when retrieving the cached class handlers. This is to maximize the reusability and performance.

```csharp
var classHandler = ClassHandlerCache.Get<Customer, CustomerClassHandler>();
```

To remove the mapping, use the `Remove()` method.

```csharp
ClassHandlerMapper.Remove<Customer>();
```

## PropertyHandler Mapping

Use the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class to manage the mappings between the property handlers and the .NET CLR type or class property.

#### Property Level

Let us say you have property handler below.

```csharp
public class CustomerAddressPropertyHandler : IPropertyHandler<string, Address>
{
    public Address Get(string input, ClassProperty property)
    {
        return !string.IsNullOrEmpty(input) ? JsonConvert.Deserialize<Address>(input) : null;
    }

    public string Set(Address input, ClassProperty property)
    {
        return input != null ? JsonConvert.Serialize(input) : null;
    }
}
```

To add a mapping, use the `Add()` method.

```csharp
PropertyHandlerMapper.Add<Customer, CustomerAddressPropertyHandler>(e => e.Address);
```

To retrieve the mapping, use the `Get()` method.

```csharp
var propertyHandler = PropertyHandlerMapper.Get<Customer, CustomerAddressPropertyHandler>(e => e.Address);
```

> We highly recommend to use the [PropertyHandlerCache](/cacher/propertyhandlercache) class when retrieving the cached property handlers. This is to maximize the reusability and performance.

```csharp
var propertyHandler = PropertyHandlerMapper.Get<Customer, CustomerAddressPropertyHandler>(e => e.Address);
```

To remove the mapping, use the `Remove()` method.

```csharp
PropertyHandlerMapper.Remove<Customer>(e => e.Address);
```

#### Type Level

Let us say you want to handle all the `System.DateTime` properties and you have created the property handler below.

```csharp
public class DateTimeKindToUtcPropertyHandler : IPropertyHandler<datetime?, datetime?>
{
    public datetime? Get(datetime? input, ClassProperty property)
    {
        return input.HasValue ? DateTime.SpecifyKind(input.Value, Kind.Utc) : null;
    }

    public datetime? Set(datetime? input, ClassProperty property)
    {
        return input.HasValue ? DateTime.SpecifyKind(input.Value, Kind.Unspecified) : null;
    }
}
```

To add a mapping, use the type-level `Add()` method.

```csharp
PropertyHandlerMapper.Add<DateTime, DateTimeKindToUtcPropertyHandler>();
```

To retrieve the mapping, use the type-level `Get()` method.

```csharp
var propertyHandler = PropertyHandlerMapper.Get<DateTime>();
```

Same here, we highly recommend to use the [PropertyHandlerCache](/cacher/propertyhandlercache) class instead.

```csharp
var propertyHandler = PropertyHandlerMapper.Get<DateTime>();
```

To remove the mapping, use the type-level `Remove()` method.

```csharp
PropertyHandlerMapper.Remove<DateTime>();
```

## Property Name Mapping

Use the [PropertyMapper](/mapper/propertymapper) class to manage the mappings of the class property into its equivalent column in the database.

Let us say you have the  `Customer` class below.

```csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set;}
    public string LastName { get; set;}
    public DateTime DateOfBirth { get; set;}
    ...
}
```

And the `[dbo].[Customer]` table below.

```csharp
CREATE TABLE [dbo].[Customer]
(
	[Id] [bigint] IDENTITY(1,1) NOT NULL,
	[FName] [nvarchar](128) NOT NULL,
	[LName] [nvarchar](128) NOT NULL,
	[DOB] [datetime2](5) NOT NULL,
	[CreatedDateUtc] [datetime2](5) NOT NULL,
	CONSTRAINT [CRIX_Customer_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

Then, use the `Add()` method to map it.

```csharp
PropertyMapper.Add<Customer>(e => e.FirstName, "[FName]");
PropertyMapper.Add<Customer>(e => e.LastName, "[LName]");
PropertyMapper.Add<Customer>(e => e.DateOfBirth, "[DOB]");
```

To retrieve the mapped name, use the `Get()` method.

```csharp
var firstName = PropertyMapper.Get<Customer>(e => e.FirstName);
var lastName = PropertyMapper.Get<Customer>(e => e.LastName);
var dob = PropertyMapper.Get<Customer>(e => e.DateOfBirth);
```

> We highly recommend to use the [PropertyMappedNameCache](/cacher/propertymappednamecache) class when retrieving the cached mapped name. This is to maximize the reusability and performance.

```csharp
var firstName = PropertyMappedNameCache.Get<Customer>(e => e.FirstName);
var lastName = PropertyMappedNameCache.Get<Customer>(e => e.LastName);
var dob = PropertyMappedNameCache.Get<Customer>(e => e.DateOfBirth);
```

To remove the mapping, use the `Remove()` method.

```csharp
PropertyMapper.Remove<Customer>(e => e.FirstName);
PropertyMapper.Remove<Customer>(e => e.LastName);
PropertyMapper.Remove<Customer>(e => e.DateOfBirth);
```

## Database Type Mapping

Use the [TypeMapper](/mapper/typemapper) class to manage the mappings of the .NET CLR type or class property into its equivalent database type.

#### Property Level

Let us say you have the `Customer` class below.

```csharp
public class Customer
{
    public int Id { get; set; }
    ...
    public DateTime DateOfBirth { get; set; }
    ...
}
```

And you would like to map the property of the `DateOfBirth` property into `DbType.DateTime2` database type.

Then, use the `Add()` method to map it.

```csharp
TypeMapper.Add<Customer>(e => e.DateOfBirth, DbType.DateTime2);
```

To retrieve the mapping, use the `Get()` method.

```csharp
var dbType = TypeMapper.Get<Customer>(e => e.DateOfBirth);
```

> We highly recommend to use the [TypeMapCache](/cacher/typemapcache) class when retrieving the cached database types. This is to maximize the reusability and performance.

```csharp
var dbType = TypeMapCache.Get<Customer>(e => e.DateOfBirth);
```

To remove the mapping, ue the `Remove()` method.

```csharp
TypeMapper.Remove<Customer>(e => e.DateOfBirth);
```

#### Type Level

Let us say you would like to always map the `System.DateTime` .NET CLR type equivalent to `DbType.DateTime2` database type.

Then, use the type-level mapping via `Add()` method.

```csharp
TypeMapper.Add<DateTime>(DbType.DateTime2);
```

To retrieve the mapping, use the type-level `Get()` method.

```csharp
var dbType = TypeMapper.Get<DateTime>();
```

Same here, we highly recommend to use the [TypeMapCache](/cacher/typemapcache) class instead.

```csharp
var dbType = TypeMapCache.Get<DateTime>();
```

To remove the mapping, use the type-level `Remove()` method.

```csharp
TypeMapper.Remove<DateTime>();
```

Please visit the [Type Mapping](/feature/typemapping) feature for further information.

## Property Value Attribute Mapping

Use the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper) class to manage the mappings of the property value attributes.

Let us say you have class handler below.

To add a mapping, use the `Add()` method.

```csharp
PropertyValueAttributeMapper.Add<Customer>(e => e.FirstName, new NameAttribute("FName"));
```

You can also map a multiple instances.

```csharp
PropertyValueAttributeMapper.Add<Customer>(e => e.FirstName, new PropertyValueAttribute[]
{
    new NameAttribute("FName"),
    new SizeAttribute(128),
    new DbTypeAttribute(DbType.AnsiString)
});
```

To retrieve the mapping, use the `Get()` method.

```csharp
var attributes = PropertyValueAttributeMapper.Get<Customer>(e => e.FirstName);
```

> We highly recommend to use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) class when retrieving the cached property value attributes. This is to maximize the reusability and performance.

```csharp
var attributes = PropertyValueAttributeCache.Get<Customer>(e => e.FirstName);
```

To remove the mapping, use the `Remove()` method.

```csharp
PropertyValueAttributeMapper.Remove<Customer>(e => e.FirstName);
```

> In the `Add()` method of all mappers, an exception will be thrown if the mapping is already exists and if you did not enfored the call using the `force` argument. Please be noted that the `force` argument is not overriding that attribute-based mapping (i.e.: by using the `System.ComponentModel.DataAnnotations.Schema` (`Table`, `Column`), [Map](/attribute/map), [Primary](/attribute/primary), [Identity](/attribute/identity), [TypeMap](/attribute/typemap), [ClassHandler](/attribute/classhandler) and [PropertyHandler](/attribute/propertyhandler)).
