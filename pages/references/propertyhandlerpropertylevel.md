---
layout: navpage
sidebar: references
title: "Property Handler for Properties Reference"
permalink: /reference/propertyhandlerpropertylevel
tags: [repodb, class, propertyhandlerpropertylevel, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Property Handler for Properties

This page contains the recommended way of implementing a property handler for property.

The consolidated output of this page can be found [here](/reference/output/propertyhandlerpropertylevel).

#### Class Creation

Create a class that inherits the [IPropertyHandler](/interface/ipropertyhandler) interface.

```csharp
public class PersonAgePropertyHandler : IPropertyHandler<int?, string>
{
    public string Get(int? input, ClassProperty property)
    {
        return input.HasValue ? $"{input.Value} years old" : "N/A";
    }

    public int? Set(string input, ClassProperty property)
    {
        return !string.IsNullOrEmpty(input) ? $"{Regex.Replace(key, "[^0-9]", string.Empty)}" : null;
    }
}
```

Use the [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) package if you are using JSON conversion.

```csharp
public class PersonAddressPropertyHandler : IPropertyHandler<string, Address>
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

#### Property Mapping

Use the [PropertyHandler](/attribute/propertyhandler) attribute to map the property handler into the class property.

```csharp
public class Person
{
    public int Id { get; set; }

    public string Name { get; set; }

    [PropertyHandler(typeof(PersonAgePropertyHandler))]
    public string Age { get; set; }

    public DateTime DateOfBirth { get; set; }

    [Map("Address"), PropertyHandler(typeof(PersonAddressPropertyHandler))]
    public Address Location { get; set; }

    public DateTime CreatedDate { get; set; }
}
```

#### Key Take-aways

- Ensure to make the `TInput` generic type `Nullable` if the database column is `NULLABLE`.
- Make the codes to both `Get` and `Set` method concise and highly performant.
- Name the property handler corresponds to its purpose.
- Mishandling of the implementation would make your application suffer in performance.
