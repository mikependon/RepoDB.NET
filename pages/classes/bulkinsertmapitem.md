---
layout: default
sidebar: classes
title: "BulkInsertMapItem"
description: "A mapping class that is being used for bulk insert operation."
permalink: /class/bulkinsertmapitem
tags: [repodb, bulkinsertmapitem]
parent: CLASSES
---

# BulkInsertMapItem

---

This class defines a column mapping between source and destination when performing bulk operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge), and [BulkUpdate](/operation/bulkupdate)).

## Create a new Instance

```csharp
var mapItem = new BulkInsertMapItem("SourceId", "DestinationId");
```

## Usage for BulkOperations

Given the following model:

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public int Age { get; set; }
    public string CreatedDate { get; set; }
}
```

And the following table structure:

```csharp
CREATE TABLE [dbo].[Person]
(
    [Id] [bigint] IDENTITY(1,1) NOT NULL,
    [Name] [nvarchar](128) NOT NULL,
    [Age] [int] NOT NULL,
    [CreatedDateUtc] [datetime2](5) NOT NULL,
    CONSTRAINT [CRIX_Person_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
)
ON [PRIMARY];
GO
```

With a method that returns an enumerable of the model:

```csharp
private IEnumerable<Person> GetPeople(int count = 10)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Person
        {
            FirstName = $"FirstName{i}",
            LastName = $"LastName{i}",
            Age = i,
            CreatedDateUtc = DateTime.UtcNow
        };
    }
}
```

You can perform a [BulkInsert](/operation/bulkinsert) with explicit column mappings:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var mappings = new []
    {
        new BulkInsertMapItem("FirstName", "FName"),
        new BulkInsertMapItem("LastName", "LName"),
        new BulkInsertMapItem("Age", "Age")
        new BulkInsertMapItem("CreatedDate", "CreatedDateUtc")
    };
    var people = GetPeople(100000);
    connection.BulkInsert(people, mappings: mappings);
}
```

{: .note }
> The same approach applies to [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge), and [BulkUpdate](/operation/bulkupdate). The `mappings` argument is optional — omitting it causes the library to auto-map columns by name (case-insensitive).
