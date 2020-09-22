---
layout: navpage
sidebar: classes
title: "DataReader"
description: "The most fastest data reader extractor in .NET. It is used to extract the content of the data reader object and map it into a class object."
permalink: /class/datareader
tags: [repodb, class, datareader, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# DataReader

This class is used to convert the `DbDataReader` object into an `IEnumerable<T>` or `IEnumerable<dynamic>` object. This is the heart of the library when it comes to data extraction from the database.

It only contains one method named `ToEnumerable`. This method is pre-compiled AOT using `Linq.Expressions`.

> This class is very high-performant and is very-efficient. It understands the schema of your database. It also reuses all the cache possibilities within the library during the extraction.

#### Extracting an Entities

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        var people = DataReader.ToEnumerable<Person>(reader);
        // Do the stuffs for 'people' here
    }
}
```

#### Extracting a Dynamics

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        var people = DataReader.ToEnumerable(reader);
        // Do the stuffs for 'people' here
    }
}
```

#### DbFields

It is also quitely import to pass the list of the [DbField](/class/dbfield) object in order for the compiler to skip the unnecessary DB-NULL checks.

To do this, simply pass the list of the DB fields in the `dbFields` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var dbFields = DbFieldCache.Get(connection, ClassMappedNameCache.Get<Person>(), null);
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        var people = DataReader.ToEnumerable<Person>(reader,
            dbFields,
            connection.GetDbSetting());
        // Do the stuffs for 'people' here
    }
}
```

> Please note to always pass the [IDbSetting](/interface/idbsetting) object when using the `dbFields` argument.