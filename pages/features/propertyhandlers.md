---
layout: navpage
sidebar: features
title: "Hints"
description: "This is the feature that would allow you to handle the tranformation of the class properties and database columns (inbound/outbound)."
permalink: /feature/propertyhandlers
tags: [repodb, class, propertyhandlers, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Property Handlers

This is the feature that would allow you to handle the tranformation of the class properties and database columns (inbound/outbound). It allows you to customize the conversion of the class properties or specific .NET CLR types.

The execution of the transformation contains the actual values and the affected class property context provided by [ClassProperty](/class/classproperty) class.

###### It uses the following objects

- [IPropertyHandler](/interface/ipropertyhandler) - an interface to mark your class as property handler.
- [PropertyHandler](/attribute/propertyhandler) - an attribute used to map to specific class property.
- [PropertyHandlerMapper](/mapper/propertyhandlermapper) - a mapper used map into a specific type.

#### Relevant Use-Cases

- A `String` to `Class` conversion. Imagine you have a column `Address` of type `NVARCHAR` and you would like it to be an `Address` class with different properties if you are extracting a record.
- A `Date` kind handler. Imagine you would like to convert the `Kind` property of the `DateTime` everytime you pull/push the record towards the database.

The scenarios can be unlimitted depends on your situation (like below).

- Overriding the monetary columns conversion into a specific .NET type.
- Querying a child records of the parent rows.
- Updating a record as a reaction to the transformation.
- Can be used as trigger.
- Manually override the default handler for the `Enumerations`.
- And many more.

#### Implementing a Property Handler

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

> The code above uses the package [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) for JSON conversion.

The property handler above is meant for a scenario of converting a `String` column type into a `Class` object.

#### Attaching to a Property

To attach a property handler into a class property, simply use the [PropertyHandller](/attribute/propertyhandler) attribute.

```csharp
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    [PropertyHandler(typeof(PersonAddressPropertyHandler))]
    public Address Address { get; set; }
}
```

When calling the pull operations (i.e.: [Query](/operation/query), [QueryAll](/operation/queryall) and [BatchQuery](/operation/batchquery)), then the `Get` method will be invoked.

On the other hand, when you call the push operations (i.e.: [Insert](/operation/insert), [Update](/operation/update) and [Merge](/operation/merge)), then the `Set` method will be invoked. 

#### Creating a Type-Level Property Handler

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

Then use the [PropertyHandlerMapper](/mapper/propertyhandlermapper) mapper class to map it.

```csharp
PropertyHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler(), true);
```