---
layout: navpage
sidebar: classes
title: "DataReader"
description: "The most fastest data reader extractor in .NET. RepoDb is using this class to extract the content of the data reader object."
permalink: /class/datareader
tags: [repodb, class, datareader, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# DataReader

This class is used to convert the `DbDataReader` object into an `IEnumerable<T>` or `IEnumerable<dynamic>` object. This is the heart of the library when it comes to data extraction from the database.

It only contains one method named `ToEnumerable`. This method is pre-compiled AOT using `Linq.Expressions`.

> This class is very high-performant and is very-efficient. It understands the schema of your database. It also reuses all the cache possibilities within the library during the extraction.

#### Extracting an Entities

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        var people = DataReader.ToEnumerable<Person>(reader, connection, null);
        // Do the stuffs for 'people' here
    }
}
```

#### Extracting a Dynamics

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        var people = DataReader.ToEnumerable(reader, connection, null);
        // Do the stuffs for 'people' here
    }
}
```

> This class is the reason why the RepoDb is the fastest .NET ORM in the world when it comes to data extraction. 