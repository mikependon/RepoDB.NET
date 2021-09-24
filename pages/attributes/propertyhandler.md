---
layout: default
sidebar: attributes
title: "PropertyHandler"
nav_order: 5
description: "Learn on how to use the RepoDB PropertyHandler attribute."
permalink: /attribute/propertyhandler
tags: [repodb, class, propertyhandler, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: ATTRIBUTES
---

# PropertyHandler

---

This attribute is used to signal a property handling operation on the class property. By having this attribute, the library compiler will automatically trigger the property handler `Get()` and `Set()` method during the serialization/deserialization and hydration process.

### Implementation

Let us say you had created a customized [IPropertyHandler](/interface/ipropertyhandler) like below.

```csharp
private class PersonAddressHandler : IPropertyHandler<string, Address>
{
    public Address Get(string input, ClassProperty property)
    {
        return JsonConvert.Deserialize<Address>(input);
    }

    public string Set(Address input, ClassProperty property)
    {
        return JsonConvert.Serialize(input);
    }
}
```

Then, you can use this attribute to mark the class property to use the `PersonAddressHandler` property handler during the transformation.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
    [PropertyHandler(typeof(PersonAddressHandler))]
    public Address Address { get; set; }
}
```

When you read the records from the database, the `Address` property is then filled by the value of the `Address` column that is being serialized as an actual class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(10045);
    Console.WriteLine($"Name: {person.Name}, Address: {person.Address.Street}, {person.Address.Region}, {person.Address.Country} ({person.Address.ZipCode})")
}
```

> The aim of this sample is to show that the property `Address` of the `Person` class is on type `NVARCHAR(MAX)` at the `[dbo].[Person]` table. In addition to this note, if this attribute is present on the property, then it ignores the automatic conversion of [TypeMapper](/mapper/typemapper#automatic) and enumerations.