---
layout: page
title: "Property Handler for Specific Type Reference Output"
permalink: /reference/output/propertyhandlertypelevel
tags: [repodb, class, propertyhandlertypelevel, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Property Handler for Specific Type

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

public class GuidToStringPropertyHandler : IPropertyHandler<Guid?, string>
{
    public string Get(Guid? input, ClassProperty property)
    {
        return input.HasValue ? input.ToString() : null;
    }

    public Guid? Set(string input, ClassProperty property)
    {
        var output = Guid.Empty;
        if (!string.IsNullOrEmpty(input) && Guid.TryParse(input, out output))
        {
            return output;
        }
        return null;
    }
}
```

##### Mappings

```csharp
PropertyHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler());
PropertyHandlerMapper.Add(typeof(Guid), new GuidToStringPropertyHandler());
```