---
layout: page
title: "Property Handler for Specific Type Reference Output (RepoDb)"
permalink: /reference/output/propertyhandlertypelevel
tags: [repodb, class, propertyhandlertypelevel, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Property Handler for Specific Type

```csharp
public class DateTimeToUtcKindPropertyHandler : IPropertyHandler<datetime?, datetime?>
{
    public datetime? Get(datetime? input, ClassProperty property)
    {
        return input.HasValue ? DateTime.SpecificKind(input.Value, Kind.Utc) : null;
    }

    public datetime? Set(datetime? input, ClassProperty property)
    {
        return input.HasValue ? DateTime.SpecificKind(input.Value, Kind.Unspecified) : null;
    }
}

PropertyTypeHandlerMapper.Add(typeof(DateTime), new DateTimeToUtcKindPropertyHandler());
```