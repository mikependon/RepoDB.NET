---
layout: page
title: "Property Handler for Specific Type Reference (RepoDb)"
permalink: /reference/propertyhandlertypelevel
tags: [repodb, class, propertyhandlertypelevel, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Property Handler for Specific Type

This page contains the recommended way of implementing a property handler for specific type.

The consolidated output of this page can be found [here](/reference/output/propertyhandlertypelevel).

> It is very important to take note that on this type of property handler, the targetting type is based on the database column (database-bound).

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
```

#### Property Mapping

Use the [PropertyTypeHandlerMapper](/mapper/propertytypehandlermapper) class to map the property handler into the class property with specific type.

```csharp
PropertyTypeHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler());
```

#### Key Take-aways

- Ensure to always make both the `TInput` and `TResult` generic types `Nullable`. It is handling both properties/columns that are nullables/non-nullables.
- Make the codes to both `Get` and `Set` method concise and highly performant.
- Name the property handler corresponds to its purpose.
- Mishandling of the implementation would make your application suffer in performance.
