---
layout: default
sidebar: interfaces
title: "IPropertyHandler"
permalink: /interface/ipropertyhandler
tags: [repodb, ipropertyhandler]
parent: INTERFACES
---

# IPropertyHandler

---

This interface marks a class as a property handler. It exposes `TInput` and `TResult` generic type parameters used by both the `Get()` and `Set()` methods.

## Generic Types

| Name | Description |
|:-----|:------------|
| TInput | Refers to the type of the database column. The input type for the getter; the output type for the setter. |
| TOutput | Refers to the type of the data entity property. The input type for the setter; the output type for the getter. |

## Methods

| Name | Description |
|:-----|:------------|
| Get | The method that is being invoked when the outbound transformation is triggered (i.e.: [Query](/operation/query), [QueryAll](/operation/queryall) and [BatchQuery](/operation/batchquery)). |
| Set | The method that is being invoked when the inbound transformation is triggered (i.e.: [Insert](/operation/insert), [Update](/operation/update), [Merge](/operation/merge) and etc). |

{: .note }
> Both methods accept the [ClassProperty](/class/classproperty) to give more context on the current method of the property handler.

## Use-Cases

This interface is useful for the following scenarios.

- Converting a JSON column into a class object.
- Handling the correct [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0) objects `Kind`.
- Overriding the monetary columns conversion into a specific .NET type.
- Querying a child records of the parent rows.
- Updating a record as a reaction to the transformation.
- Can be used as trigger.
- Manually override the default handler for the Enumerations.

{: .important }
> The use-cases are unlimited depending on the situation. Additionally, mapping a property handler to a property overrides the automatic conversion of [TypeMapper](/mapper/typemapper#automatic) and enumerations.

## How to Implement?

Create a class that implements this interface.

```csharp
public class AddressPropertyHandler : IPropertyHandler<string, Address>
{
    public Address Get(string input, PropertyHandlerGetOptions options)
    {
        // Handle the transformation from the DB towards the Class
    }

    public string Set(Address input, PropertyHandlerSetOptions options)
    {
        // Handle the transformation from the Class towards the DB
    }
}
```

## Property Level Handling

Property-level transformation is useful when a specific column requires custom handling. For example, given a `[dbo].[Person]` table where the `Address` column is of type `NVARCHAR(MAX)`:

**Classes**

```csharp
public class Address
{
    public int HouseNo { get; set; }
    public string Country { get; set; }
    public string State { get; set; }
    public string Street { get; set; }
    public string Region { get; set; }
    public int ZipCode { get; set; }
}
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    public Address Address { get; set; }
}
```

**Handler**

```csharp
public class AddressPropertyHandler : IPropertyHandler<string, Address>
{
    public Address Get(string input, PropertyHandlerGetOptions options)
    {
        return JsonConvert.DeserializeObject<Address>(input);
    }

    public string Set(Address input, PropertyHandlerSetOptions options)
    {
        return JsonConvert.SerializeObject(input);
    }
}
```

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.Query<Person>(10045);
	Console.WriteLine($"Name: {person.Name}, Address: {person.Address.Street}, {person.Address.Region}, {person.Address.Country} ({person.Address.ZipCode})")
}
```

**Mapping**

Via the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class.

```csharp
PropertyHandlerMapper
    .Add<Person, AddressPropertyHandler>(e => e.Address, true);
```

Or, via the [FluentMapper](/mapper/fluentmapper) class.

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyHandler<AddressPropertyHandler>(e => e.Address, true);
```

Or, via an explicit [ClassHandler](/attribute/classhandler) attribute.

```csharp
publi class Person
{
    ...

    [ClassHandler(typeof(AddressPropertyHandler))]
    public Address Address { get; set; }

    ...
}
```

{: .note }
> When you call any of the fetch ([Query](/operation/query), [QueryAll](/operation/queryall) and [BatchQuery](/operation/batchquery)) or push ([Insert](/operation/insert), [Update](/operation/update), [Merge](/operation/merge)) operations, the methods `Get()` and `Set()`  of the property handler will be invoked immediately.

## Type Level Handling

Type-level handling applies a transformation to all properties of a given .NET CLR type across the entire application. This is useful for cross-cutting concerns such as normalizing [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0) `Kind` values on every read.

Use the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class to register the mapping.

The following example converts all `DateTime` properties to UTC on every read operation.

**Handler**

```csharp
public class DateTimeKindToUtcPropertyHandler : IPropertyHandler<DateTime?, DateTime?>
{
    public DateTime? Get(DateTime? input, PropertyHandlerGetOptions options)
    {
        // Reading from DB, setting the class
        return input.HasValue ? DateTime.SpecifyKind(input.Value, DateTimeKind.Utc) : null;
    }

    public DateTime? Set(DateTime? input, PropertyHandlerSetOptions options)
    {
        // Reading from class, setting back to DB
        return input.HasValue ? DateTime.SpecifyKind(input.Value, DateTimeKind.Unspecified) : null;
    }
}
```

**Mapping**

Via the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class.

```csharp
PropertyHandlerMapper
    .Add<DateTime, DateTimeKindToUtcPropertyHandler>(true);
```

Or, via the [FluentMapper](/mapper/fluentmapper) class.

```csharp
FluentMapper
    .Type<DateTime>()
    .PropertyHandler<DateTimeKindToUtcPropertyHandler>(true);
```
