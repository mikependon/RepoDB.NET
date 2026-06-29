---
layout: default
sidebar: classes
title: "DataReader"
description: "The most fastest data reader extractor in .NET. It is used to extract the content of the data reader object and map it into a class object."
permalink: /class/datareader
tags: [repodb, datareader]
parent: CLASSES
---

# DataReader

---

Converts a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) into an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0). It is the core of the library's data extraction pipeline.

It exposes a single method, `ToEnumerable`, which is pre-compiled AOT using `Linq.Expressions`.

{: .note }
> This class caches the database schema and reuses all library-level caches during extraction to produce the most efficient AOT compilation.

## Extract as Entity Model

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        var people = DataReader.ToEnumerable<Person>((DbDataReader) reader);
        // Do the stuffs for 'people' here
    }
}
```

## Extract as Dynamic/ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        var people = DataReader.ToEnumerable((DbDataReader) reader);
        // Do the stuffs for 'people' here
    }
}
```

## DbFields

Passing a list of [DbField](/class/dbfield) objects allows the compiler to skip unnecessary DB-NULL checks.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var dbFields = DbFieldCache.Get(connection, ClassMappedNameCache.Get<Person>(), null);
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        var people = DataReader.ToEnumerable<Person>((DbDataReader) reader,
            dbFields,
            connection.GetDbSetting());
        // Do the stuffs for 'people' here
    }
}
```

{: .note }
> Always pass the [IDbSetting](/interface/idbsetting) object when using the `dbFields` argument.
