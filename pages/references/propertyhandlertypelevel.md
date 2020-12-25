---
layout: default
sidebar: references
title: "Property Handler (Type Level)"
nav_order: 8
permalink: /reference/propertyhandlertypelevel
tags: [repodb, class, propertyhandlertypelevel, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: References
---

# Property Handler (Type Level)

---

This page contains the recommended way of implementing a property handler for specific type.

> It is important to take note that this property handler is based on database column type (database-bound).

### PropertyHandler

Create a class that implements the [IPropertyHandler](/interface/ipropertyhandler) interface.

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

### Mapping

#### Explict

Use the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class to map the property handlers into the target types.

```csharp
PropertyHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler());
PropertyHandlerMapper.Add(typeof(Guid), new GuidToStringPropertyHandler());
```

#### Implicit

Use the [FluentMapper](/mapper/fluentmapper) class to map the property handlers into the target types.

```csharp
FluentMapper
    .Type<DateTime>()
    .PropertyHandler<DateTimeKindToUtcPropertyHandler>();

FluentMapper
    .Type<Guid>()
    .PropertyHandler<GuidToStringPropertyHandler>();
```

### Key Take-aways

- Ensure to always make both the `TInput` and `TResult` generic types `nullable`. It is handling both the properties/columns that are nullables/non-nullables.
- Make the code snippets to both `Get()` and `Set()` method concise and highly performant.
- Name the property handler corresponds to its purpose.
- Mishandling of the implementation would make your application suffer in performance.
