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

This is a feature that would allow you to handle the tranformation of the class properties and database columns (inbound/outbound). It allows you to customize the conversion of the class properties and the .NET CLR types.

The execution of the transformation contains the actual values and the affected [ClassProperty](/class/classproperty) object to provide more context.

Below is the high-level diagram of the property handler.

<img src="../../assets/images/site/propertyhandler.svg" />

#### It uses the following objects

- [IPropertyHandler](/interface/ipropertyhandler) - an interface to mark your class as property handler.
- [PropertyHandler](/attribute/propertyhandler) - an attribute used to map a property handler into a specific property.
- [PropertyHandlerMapper](/mapper/propertyhandlermapper) - a mapper used to map into a property handler into a specific property.
- [FluentMapper](/mapper/fluentmapper) - a fluent mapper class used to map into a property handler into a specific property.

### Relevant Use-Cases

- A string to class conversion. Imagine you have a column `Address` of type `NVARCHAR` and you would like it to be an `Address` type/class within your application.
- A date kind handler. Imagine you would like to convert the `Kind` property of the `DateTime` object everytime you pull/push the record towards the database.

The scenarios can be unlimitted depends on your own situation (like below).

- Overriding the monetary columns conversion into a specific .NET type.
- Querying the related child records of the parent rows.
- Updating a record as a reaction to the transformation.
- Can be used as trigger.
- Manually override the default handler for the enumerations.
- And many more.

### Implementing a Property Handler

Create a class that implements the [IPropertyHandler](/interface/ipropertyhandler) interface.

```csharp
public class PersonAddressPropertyHandler : IPropertyHandler<string, Address>
{
    public Address Get(string input, ClassProperty property)
    {
        if (!string.IsNullOrEmpty(input))
        {
            return JsonConvert.Deserialize<Address>(input);
        }
        return null;
    }

    public string Set(Address input, ClassProperty property)
    {
        if (input != null)
        {
            return JsonConvert.Serialize(input);
        }
        return null;
    }
}
```

The property handler above is meant for a scenario of converting a string column type into a class object.

> The code above uses the package [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) for JSON conversion.

### Attaching to a Property

To attach a property handler into a class property, simply use the [PropertyHandller](/attribute/propertyhandler) attribute.

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    [PropertyHandler(typeof(PersonAddressPropertyHandler))]
    public Address Address { get; set; }
}
```

Or, use the [FluentMapper](/mapper/fluentmapper) class. It uses the [PropertyHandlerMapper](/mapper/propertyhandlermapper) underneath.

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyHandler<PersonAddressPropertyHandler>(e => e.Address);
```

When calling the pull operations (i.e.: [Query](/operation/query), [QueryAll](/operation/queryall) and [BatchQuery](/operation/batchquery)), then the `Get()` method will be invoked.

On the other hand, when you call the push operations (i.e.: [Insert](/operation/insert), [Update](/operation/update) and [Merge](/operation/merge)), then the `Set()` method will be invoked. 

> Please visit our [Property Handler (Property Level)](/reference/propertyhandlerpropertylevel) reference implementation page for the detailed implementation.

### Creating a Type-Level Property Handler

The process is the same as with [Implementing a Property Handler](#implementing-a-property-handler) section. Create a class that implements the [IPropertyHandler](/interface/ipropertyhandler) interface.

```csharp
public class DateTimeKindToUtcPropertyHandler : IPropertyHandler<DateTime?, DateTime?>
{
    public DateTime? Get(DateTime? input, ClassProperty property)
    {
        return DateTime.SpecifyKind(input, DateTimeKind.Utc);
    }

    public DateTime? Set(DateTime? input, ClassProperty property)
    {
        return DateTime.SpecifyKind(input.GetValueOrDefault(), DateTimeKind.Unspecified);
    }
}
```

Then, use the [PropertyHandlerMapper](/mapper/propertyhandlermapper) mapper class to map it.

```csharp
PropertyHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler(), true);
```

Or, use the [FluentMapper](/mapper/fluentmapper) class type level mapping. Again, it also uses the [PropertyHandlerMapper](/mapper/propertyhandlermapper) underneath.

```csharp
FluentMapper
    .Type<DateTime>()
    .PropertyHandler<PersonAddressPropertyHandler>();
```

> Please visit our [Property Handler (Type Level)](/reference/propertyhandlertypelevel) reference implementation page for the detailed implementation.
