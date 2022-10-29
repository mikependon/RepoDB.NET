---
layout: default
sidebar: references
title: "Property Handler (Property Level)"
nav_order: 7
permalink: /reference/propertyhandlerpropertylevel
tags: [repodb, propertyhandlerpropertylevel]
parent: REFERENCES
---

# Property Handler (Property Level))

---

This page contains the recommended way of implementing a property handler for the class properties.

## PropertyHandler

Create a class that implements the [IPropertyHandler](/interface/ipropertyhandler) interface.

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

## Mapping

#### Explict

Use the [PropertyHandler](/attribute/propertyhandler) attribute to map the property handlers into the class properties.

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

#### Implicit

Use the [FluentMapper](/mapper/fluentmapper) class to map the property handlers into the class properties.

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyHandler<PersonAgePropertyHandler>(e => e.Age)
    .Column(e => e.Location, "Address")
    .PropertyHandler<PersonAddressPropertyHandler>(e => e.Location);
```

## Key Take-aways

- Ensure to make the `TInput` generic type `nullable` if the database column is `nullable`.
- Make the code snippets to both `Get()` and `Set()` methods concise and highly performant.
- Name the property handlers as correspond into its own purpose.
- Mishandling of the implementation would make your application suffer in performance.
