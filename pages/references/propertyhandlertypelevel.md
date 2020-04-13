---
layout: navpage
sidebar: references
title: "Property Handler for Specific Type Reference (RepoDb)"
permalink: /reference/propertyhandlertypelevel
tags: [repodb, class, propertyhandlertypelevel, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Property Handler for Specific Type

This page contains the recommended way of implementing a property handler for specific type.

The consolidated output of this page can be found [here](/reference/output/propertyhandlertypelevel).

> It is important to take note that this property handler is based on database column type (database-bound).

#### Class Creation

Create a class that inherits the [IPropertyHandler](/interface/ipropertyhandler) interface.

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

#### Property Mapping

Use the [PropertyHandlerMapper](/mapper/propertytypehandlermapper) class to map the property handler into the class property with specific type.

```csharp
PropertyHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler());
PropertyHandlerMapper.Add(typeof(Guid), new GuidToStringPropertyHandler());
```

#### Key Take-aways

- Ensure to always make both the `TInput` and `TResult` generic types `Nullable`. It is handling both properties/columns that are nullables/non-nullables.
- Make the codes to both `Get` and `Set` method concise and highly performant.
- Name the property handler corresponds to its purpose.
- Mishandling of the implementation would make your application suffer in performance.
