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

This feature allows you to implicitly map .NET CLR types or class properties to their equivalent database objects. Dedicated mapper classes are provided as an alternative to using attributes directly on the classes.

## Fluent Mapping

Use the [FluentMapper](/mapper/fluentmapper) class to manage table/property mappings, primary/identity columns, database types, and class/property handlers in a fluent style.

To define mappings for an entity, use the `Entity()` method:

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

To define mappings for a specific .NET CLR type, use the `Type()` method:

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

Mapping priority is determined first at the attribute level, then at the property level, then at the type level.

{: .note }
> [FluentMapper](/mapper/fluentmapper) uses the following classes internally to establish mappings: [ClassMapper](/mapper/classmapper), [ClassHandlerMapper](/mapper/classhandlermapper), [IdentityMapper](/mapper/identitymapper), [PrimaryMapper](/mapper/primarymapper), [PropertyHandlerMapper](/mapper/propertyhandlermapper), [PropertyMapper](/mapper/propertymapper), and [TypeMapper](/mapper/typemapper).

## Class Name Mapping

Use the [ClassMapper](/mapper/classmapper) class to map a class to its equivalent database object (table or view).

Given a `Customer` class with an equivalent table named `Customer` under the `sales` schema:

```csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set;}
    public string LastName { get; set;}
    ...
}
```

Use the `Add()` method to map it:

```csharp
ClassMapper.Add<Customer>("[sales].[Customer]");
```
To retrieve the mapped name, use the `Get()` method:

```csharp
var mappedName = ClassMapper.Get<Customer>();
```

{: .important }
> Use [ClassMappedNameCache](/cacher/classmappednamecache) when retrieving the mapped name to maximize reusability and performance.

```csharp
var mappedName = ClassMappedNameCache.Get<Customer>();
```

To remove the mapping, use the `Remove()` method:

```csharp
ClassMapper.Remove<Customer>();
```

## Identity Mapping

Use the [IdentityMapper](/mapper/identitymapper) class to map a class property as an identity.

To add a mapping:

```csharp
IdentityMapper.Add<Customer>(e => e.Id);
```

To retrieve the mapping (returns a [ClassProperty](/class/classproperty) instance):

```csharp
var property = IdentityMapper.Get<Customer>();
```

{: .important }
> Use [IdentityCache](/cacher/identitycache) when retrieving the identity property to maximize reusability and performance.

```csharp
var property = IdentityCache.Get<Customer>();
```

To remove the mapping:

```csharp
IdentityMapper.Remove<Customer>();
```

## Primary Mapping

Use the [PrimaryMapper](/mapper/primarymapper) class to map a class property as a primary key.

To add a mapping:

```csharp
PrimaryMapper.Add<Customer>(e => e.Id);
```
To retrieve the mapping (returns a [ClassProperty](/class/classproperty) instance):

```csharp
var property = PrimaryMapper.Get<Customer>();
```

{: .important }
> Use [PrimaryCache](/cacher/primarycache) when retrieving the primary property to maximize reusability and performance.

```csharp
var mappedName = PrimaryCache.Get<Customer>();
```

To remove the mapping:

```csharp
PrimaryMapper.Remove<Customer>();
```

## ClassHandler Mapping

Use the [ClassHandlerMapper](/mapper/classhandlermapper) class to map a class handler to a .NET CLR type.

Given the following class handler:

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

To add a mapping:

```csharp
ClassHandlerMapper.Add<Customer, CustomerClassHandler>();
```

To retrieve the mapping:

```csharp
var classHandler = ClassHandlerMapper.Get<Customer, CustomerClassHandler>();
```

{: .important }
> Use [ClassHandlerMapper](/mapper/classhandlermapper) when retrieving cached class handlers to maximize reusability and performance.

```csharp
var classHandler = ClassHandlerCache.Get<Customer, CustomerClassHandler>();
```

To remove the mapping:

```csharp
ClassHandlerMapper.Remove<Customer>();
```

## PropertyHandler Mapping

Use the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class to map property handlers to a .NET CLR type or class property.

To map a property handler to an entity model property, given the following handler:

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

To add a mapping:

```csharp
PropertyHandlerMapper.Add<Customer, CustomerAddressPropertyHandler>(e => e.Address);
```

To retrieve the mapping:

```csharp
var propertyHandler = PropertyHandlerMapper.Get<Customer, CustomerAddressPropertyHandler>(e => e.Address);
```

{: .important }
> Use [PropertyHandlerCache](/cacher/propertyhandlercache) when retrieving cached property handlers to maximize reusability and performance.

```csharp
var propertyHandler = PropertyHandlerMapper.Get<Customer, CustomerAddressPropertyHandler>(e => e.Address);
```

To remove the mapping:

```csharp
PropertyHandlerMapper.Remove<Customer>(e => e.Address);
```

To define a type-level property handler mapping for all [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0) properties, given the following handler:

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

To add a type-level mapping:

```csharp
PropertyHandlerMapper.Add<DateTime, DateTimeKindToUtcPropertyHandler>();
```

To retrieve the type-level mapping:

```csharp
var propertyHandler = PropertyHandlerMapper.Get<DateTime>();
```

Use [PropertyHandlerCache](/cacher/propertyhandlercache) instead for better performance:

```csharp
var propertyHandler = PropertyHandlerMapper.Get<DateTime>();
```

To remove the type-level mapping:

```csharp
PropertyHandlerMapper.Remove<DateTime>();
```

## Property Name Mapping

Use the [PropertyMapper](/mapper/propertymapper) class to map class properties to their equivalent database columns.

Given the following `Customer` class:

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

And the following `[dbo].[Customer]` table:

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

Use the `Add()` method to map the properties:

```csharp
PropertyMapper.Add<Customer>(e => e.FirstName, "[FName]");
PropertyMapper.Add<Customer>(e => e.LastName, "[LName]");
PropertyMapper.Add<Customer>(e => e.DateOfBirth, "[DOB]");
```

To retrieve the mapped names:

```csharp
var firstName = PropertyMapper.Get<Customer>(e => e.FirstName);
var lastName = PropertyMapper.Get<Customer>(e => e.LastName);
var dob = PropertyMapper.Get<Customer>(e => e.DateOfBirth);
```

{: .important }
> Use [PropertyMappedNameCache](/cacher/propertymappednamecache) when retrieving cached mapped names to maximize reusability and performance.

```csharp
var firstName = PropertyMappedNameCache.Get<Customer>(e => e.FirstName);
var lastName = PropertyMappedNameCache.Get<Customer>(e => e.LastName);
var dob = PropertyMappedNameCache.Get<Customer>(e => e.DateOfBirth);
```

To remove the mappings:

```csharp
PropertyMapper.Remove<Customer>(e => e.FirstName);
PropertyMapper.Remove<Customer>(e => e.LastName);
PropertyMapper.Remove<Customer>(e => e.DateOfBirth);
```

## Database Type Mapping

Use the [TypeMapper](/mapper/typemapper) class to map .NET CLR types or class properties to their equivalent database types.

#### Property Level

Given the following `Customer` class:

```csharp
public class Customer
{
    public int Id { get; set; }
    ...
    public DateTime DateOfBirth { get; set; }
    ...
}
```

To map `DateOfBirth` to `DbType.DateTime2`:

```csharp
TypeMapper.Add<Customer>(e => e.DateOfBirth, DbType.DateTime2);
```

To retrieve the mapping:

```csharp
var dbType = TypeMapper.Get<Customer>(e => e.DateOfBirth);
```

{: .important }
> Use [TypeMapCache](/cacher/typemapcache) when retrieving cached database types to maximize reusability and performance.

```csharp
var dbType = TypeMapCache.Get<Customer>(e => e.DateOfBirth);
```

To remove the mapping:

```csharp
TypeMapper.Remove<Customer>(e => e.DateOfBirth);
```

#### Type Level

To map all [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0) values to `DbType.DateTime2`:

```csharp
TypeMapper.Add<DateTime>(DbType.DateTime2);
```

To retrieve the mapping:

```csharp
var dbType = TypeMapper.Get<DateTime>();
```

Use [TypeMapCache](/cacher/typemapcache) instead for better performance:

```csharp
var dbType = TypeMapCache.Get<DateTime>();
```

To remove the type-level mapping:

```csharp
TypeMapper.Remove<DateTime>();
```

Please visit the [Type Mapping](/feature/typemapping) feature for further information.

## Property Value Attribute Mapping

Use the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper) class to manage property value attribute mappings.

To add a mapping:

```csharp
PropertyValueAttributeMapper.Add<Customer>(e => e.FirstName, new NameAttribute("FName"));
```

Multiple attributes can be mapped at once:

```csharp
PropertyValueAttributeMapper.Add<Customer>(e => e.FirstName, new PropertyValueAttribute[]
{
    new NameAttribute("FName"),
    new SizeAttribute(128),
    new DbTypeAttribute(DbType.AnsiString)
});
```

To retrieve the mapping:

```csharp
var attributes = PropertyValueAttributeMapper.Get<Customer>(e => e.FirstName);
```

{: .important }
> Use [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) when retrieving cached property value attributes to maximize reusability and performance.

```csharp
var attributes = PropertyValueAttributeCache.Get<Customer>(e => e.FirstName);
```

To remove the mapping:

```csharp
PropertyValueAttributeMapper.Remove<Customer>(e => e.FirstName);
```

{: .note }
The `force` argument does not override attribute-based mappings (e.g., via [System.ComponentModel.DataAnnotations.Schema](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.schema?view=net-6.0) ([Table](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.schema.tableattribute?view=net-6.0), `Column`), [Map](/attribute/map), [Primary](/attribute/primary), [Identity](/attribute/identity), [TypeMap](/attribute/typemap), [ClassHandler](/attribute/classhandler) and [PropertyHandler](/attribute/propertyhandler)).

{: .warning }
> In the `Add()` method of all mappers, an exception is thrown if the mapping already exists and the `force` argument was not set.
