---
layout: default
sidebar: attributes
title: "PropertyHandler"
nav_order: 5
description: "Learn on how to use the RepoDB PropertyHandler attribute."
permalink: /attribute/propertyhandler
tags: [repodb, propertyhandler]
parent: ATTRIBUTES
---

# PropertyHandler

---

This attribute enables property handling on a class property. When present, the library compiler automatically invokes the property handler's `Get()` and `Set()` methods during serialization, deserialization, and hydration.

Given a customized [IPropertyHandler](/interface/ipropertyhandler) implementation:

```csharp
private class PersonAddressHandler : IPropertyHandler<string, Address>
{
    public Address Get(string input, PropertyHandlerGetOptions options)
    {
        return JsonConvert.Deserialize<Address>(input);
    }

    public string Set(Address input, PropertyHandlerSetOptions options)
    {
        return JsonConvert.Serialize(input);
    }
}
```

Apply this attribute to the property to use `PersonAddressHandler` during transformation.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    [PropertyHandler(typeof(PersonAddressHandler))]
    public Address Address { get; set; }
}
```

When reading records, the `Address` property is populated with the deserialized value of the `Address` column.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(10045);
    Console.WriteLine($"Name: {person.Name}, Address: {person.Address.Street}, {person.Address.Region}, {person.Address.Country} ({person.Address.ZipCode})")
}
```

{: .note }
> In this example, the `Address` property maps to an `NVARCHAR(MAX)` column in `[dbo].[Person]`. When this attribute is present, the library skips automatic conversion from [TypeMapper](/mapper/typemapper#automatic) and enumerations.