---
layout: page
title: "Property Handler for Properties Reference Output"
permalink: /reference/output/propertyhandlerpropertylevel
tags: [repodb, class, propertyhandlerpropertylevel, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Property Handler for Properties

The property handlers below is using the [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) package.

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

public class PersonAddressPropertyHandler : IPropertyHandler<string, string>
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