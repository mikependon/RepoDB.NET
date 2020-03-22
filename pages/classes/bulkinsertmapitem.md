---
layout: page
title: "BulkInsertMapItem (RepoDb)"
permalink: /class/bulkinsertmapitem
tags: [repodb, class, bulkinsertmapitem, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## BulkInsertMapItem

This class is used to map a source and destination column when doing Bulk Operations (ie: [BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)).

> In this tutorial, we will use the [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer) as the database and [C#](https://docs.microsoft.com/en-us/dotnet/csharp/) as the programming language.

#### Create a new Instance

Below is simple way to create an instance.

```csharp
var mapItem = new BulkInsertMapItem("SourceId", "DestinationId");
```

#### Usage for BulkOperations

Let us say you a model class like below.

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

And you have a table structure like below.

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

Then you created a method that does return an array of your model.

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

Then you can do [BulkInsert](/operation/bulkinsert) with the mappings below.

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

> You can also do the same for (ie: [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)). Please also be reminded that the `mappings` argument are not mandatory. Leaving this blank would make the Bulk Operations auto-map the columns based on the equality of the source and destination column-names (case-insensitive).