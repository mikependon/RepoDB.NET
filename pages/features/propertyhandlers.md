---
layout: default
sidebar: features
title: "Property Handlers"
description: "This is a feature that would allow you to handle the tranformation of the class properties and database columns (inbound/outbound)."
permalink: /feature/propertyhandlers
tags: [repodb, propertyhandlers]
parent: FEATURES
---

# Property Handlers

---

This feature allows you to handle the transformation of class properties and database columns (inbound/outbound). It enables custom conversion between class properties and .NET CLR types.

Each transformation execution receives the actual value and the affected [ClassProperty](/class/classproperty) object for additional context.

The following objects are used:

| Object | Description  | 
|:-------------|:-------------|
| [IPropertyHandler](/interface/ipropertyhandler) | An interface to mark your class as a property handler. |
| [PropertyHandler](/attribute/propertyhandler) | An attribute used to map a property handler to a specific property. |
| [PropertyHandlerMapper](/mapper/propertyhandlermapper) | A mapper used to map a property handler to a specific property. |
| [FluentMapper](/mapper/fluentmapper) | A fluent mapper class used to map a property handler to a specific property. |

## Relevant Use-Cases

Common scenarios that this feature addresses:

| Use-Case | Description  | 
|:-------------|:-------------|
| String to Complex-Type | Convert an `NVARCHAR` column named `Address` to and from an `Address` class within the application. |
| As Type Handler | Convert the `Kind` property of [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0) each time a record is pushed or pulled from the database. |

Additional use cases include:

- Overriding the conversion of monetary columns to a specific .NET type.
- Querying related child records of parent rows.
- Triggering updates as a reaction to transformation.
- Using as a trigger mechanism.
- Manually overriding the default handler for enumerations.
- And many more.

## How does it works?

When reading data from the database (e.g., [ExecuteQuery](/operation/executequery), [Query](/operation/query), [BatchQuery](/operation/batchquery)), the `Get()` method is invoked after deserializing the model property. When pushing data to the database (e.g., [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update)), the `Set()` method is invoked before the actual database operation.

## Implementing a Property Handler

Create a class that implements the [IPropertyHandler](/interface/ipropertyhandler) interface.

```csharp
public class PersonAddressPropertyHandler : IPropertyHandler<string, Address>
{
    public Address Get(string input, PropertyHandlerGetOptions options) =>
        !string.IsNullOrEmpty(input) ? JsonConvert.Deserialize<Address>(input) : null;

    public string Set(Address input, PropertyHandlerSetOptions options) =>
        (input != null) ? JsonConvert.Serialize(input) : null;
}
```

The handler above converts a string column to a class object and back.

## Attaching to a Property

Use the [PropertyHandler](/attribute/propertyhandler) attribute to attach a property handler to a class property.

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    [PropertyHandler(typeof(PersonAddressPropertyHandler))]
    public Address Address { get; set; }
}
```

Or use the [FluentMapper](/mapper/fluentmapper) class, which uses [PropertyHandlerMapper](/mapper/propertyhandlermapper) internally.

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyHandler<PersonAddressPropertyHandler>(e => e.Address);
```

When calling pull operations (e.g., [Query](/operation/query), [QueryAll](/operation/queryall), [BatchQuery](/operation/batchquery)), the `Get()` method is invoked.

When calling push operations (e.g., [Insert](/operation/insert), [Update](/operation/update), [Merge](/operation/merge)), the `Set()` method is invoked.

{: .note }
> Please visit our [Property Handler (Property Level)](/reference/propertyhandlerpropertylevel) reference implementation page for the detailed implementation.

## Creating a Type-Level Property Handler

The implementation follows the same process as [Implementing a Property Handler](#implementing-a-property-handler). Create a class that implements the [IPropertyHandler](/interface/ipropertyhandler) interface.

```csharp
public class DateTimeKindToUtcPropertyHandler : IPropertyHandler<DateTime?, DateTime?>
{
    public DateTime? Get(DateTime? input, PropertyHandlerGetOptions options) =>
        DateTime.SpecifyKind(input, DateTimeKind.Utc);

    public DateTime? Set(DateTime? input, PropertyHandlerSetOptions options) =>
        DateTime.SpecifyKind(input.GetValueOrDefault(), DateTimeKind.Unspecified);
}
```

Use the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class to map it:

```csharp
PropertyHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler(), true);
```

Or use the [FluentMapper](/mapper/fluentmapper) class for type-level mapping, which also uses [PropertyHandlerMapper](/mapper/propertyhandlermapper) internally.

```csharp
FluentMapper
    .Type<DateTime>()
    .PropertyHandler<PersonAddressPropertyHandler>();
```

{: .note }
> Please visit our [Property Handler (Type Level)](/reference/propertyhandlertypelevel) reference implementation page for the detailed implementation.
