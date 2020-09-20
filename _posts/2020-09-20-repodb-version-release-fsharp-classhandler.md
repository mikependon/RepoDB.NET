---
layout: post
title: "Announcing RepoDB version 1.12.0"
author: "Michael Camara Pendon"
date: 2020-09-28 00:00:00 +0200
categories: blogs repodb
---

Today, we are announcing the availability of [RepoDb v1.12.0](https://www.nuget.org/packages/RepoDb/1.12.0). The released packages also comes with its bundles, the extension libraries in v1.1.0. See below.

- [RepoDb.SqlServer v1.1.0](https://www.nuget.org/packages/RepoDb.SqlServer/1.1.0)
- [RepoDb.SqlServer.BulkOperations v1.1.0](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations/1.1.0)
- [RepoDb.SqLite v1.1.0](https://www.nuget.org/packages/RepoDb.SqLite/1.1.0)
- [RepoDb.MySql v1.1.0](https://www.nuget.org/packages/RepoDb.MySql/1.1.0)
- [RepoDb.MySqlConnector v1.1.0](https://www.nuget.org/packages/RepoDb.MySqlConnector/1.1.0)
- [RepoDb.PostgreSql v1.1.0](https://www.nuget.org/packages/RepoDb.PostgreSql/1.1.0)

### TL;DR;

The version 1.12.0 / v1.1.0 (extensions) releases are one of the biggest suite release of RepoDB. It contains the important updates to its features and capabilities. It also includes some Major Enhancements, Bug Fixes and important requests from the .NET community. It has the initial complete support to F# programming language and is bundled with the new Enhanced Compiler, Class Handler and Advanced Dynamic Operation Invocations.

### Highlights

- [TL;DR;](#tldr)
- [Highlights](#highlights)
- [Complete Support to FSharp](#complete-support-to-fsharp)
- [Enhanced Compiler](#enhanced-compiler)
- [Class Handler](#class-handler)
- [Immutable Classes](#immutable-classes)
- [Anonymous Type / ExpandoObject / IDictionary<string, object>](#anonymous-type--expandoobject--idictionarystring-object)
- [Table-Based Fluent Calls](#table-based-fluent-calls)
- [The 'fields' argument](#the-fields-argument)
- [Typed Result Execution](#typed-result-execution)
- [Support to Table-Valued Parameters (TVP)](#support-to-table-valued-parameters-tvp)
- [Breaking Changes](#breaking-changes)
  - [Merge 'qualifiers' and 'fields' argument](#merge-qualifiers-and-fields-argument)
  - [The 'Where/WhereOrPrimaryKey' vs 'What'](#the-wherewhereorprimarykey-vs-what)
- [Closing Note](#closing-note)

It is also important to take note of the [Breaking Changes](#breaking-changes).

### Complete Support to FSharp

The community requests and the initial complete support to F# programming language is part of the package.

First, install the package.

```csharp
> Install-Package RepoDb.SqlServer
```

Then, initialize the bootstrapper.

```csharp
> RepoDb.SqlServerBootstrap.Initialize();
```

Then, create a model.

```csharp
module Types =
    //[<CLIMutable>]
    type Person = 
        { Id: int64
          Name: string
          Age: int
          Address: string
          IsActive: bool }
```

And also the table.

```csharp
CREATE TABLE [dbo].[Person]
(
	[Id] [bigint] PRIMARY KEY IDENTITY(1,1) NOT NULL,
	[Name] [nvarchar](128) NOT NULL,
	[Age] [int] NULL,
	[Address] [nvarchar](max) NULL,
	[IsActive] [bit] NOT NULL
)
GO

ALTER TABLE [dbo].[Person] ADD  CONSTRAINT [DF_Person_IsActive]  DEFAULT ((1)) FOR [IsActive]
GO
```

Lastly, call the operations.

```csharp
// Query
let connection = (new SqlConnection(url)).EnsureOpen()
let queryResult = connection.QueryAll("Person").AsList()

// Insert
let person = { Id = 0L; Name = "John Doe"; Address = "New York"; Age = 32; IsActive = true }
let id = connection.Insert<Person, int64>(person)

// Insert (TableName)
let person = {| Name = "James Smith"; Age = 32; Address = "Washington"; IsActive = true|}
let id = connection.Insert<int64>(ClassMappedNameCache.Get<Person>(), person)

// Merge
let person = { Id = 0L; Name = "John Doe"; }
let id = connection.Merge<Person>(person)

// Update
let person = { Id = 0L; Name = "James Doe"; }
let affectedRows = connection.Update<Person>(person)

// Delete
let affectedRows = connection.Delete<Person>(10045)

```

All the functionalities written for C# programming language is also inheritted by F#. It is also important to take note that the `CLIMutable` attribute can be eliminated to all your models starting from this release.

**Note:** The option types in F# is not supported if it is being placed at the [ClassHandler](/feature/classhandlers) and [PropertyHandler](/feature/propertyhandlers) objects.

### Enhanced Compiler

The core compiler of RepoDB has been rewritten from being a monilithic code base to a much more smallers (easy, neat and cleaner) code base. All the classes needed for AOT compilation has been separated as a partial classes.

The reasons and the motivations behind this are the following.

- The old compiler code is harder to maintain and RepoDB is becoming more complex. The new compiler is easy, simple, neat and clean. It adheres to the clean-code principles.
- If the Conversion is Automatic, the old compiler does multiple conversion of the expression, thus slowing down the process.
- There are multiple repeatitive code in the old compiler which has been completely eliminated.
- To simplify the error handling. It was also introduced on most critical part of the new compiler, thus give a better error messages to the user of the library.
- Difficulty of the implementation to the extended functionality (i.e.: [ClassHandler](/feature/classhandlers), [PropertyHandler](/feature/propertyhandlers)). It is very easy to do such things with the new compiler.

In addition to this, with the new compiler, the Anonymous Type Param Argument is now being cached and being ahead-of-time (AOT) compiled. Meaning, the calls to the `Execute` methods (i.e.: [ExecuteQuery](/operation/executequery), [ExecuteNonQuery](/operation/executenonquery), [ExecuteScalar](/operation/executescalar), [ExecuteReader](/operation/executereader) and [ExecuteQueryMultiple](/operation/executequerymultiple)) will be much more optimal and efficient than before if using the Anonymous Type. See below.

```csharp
connection.ExecuteQuery<Person>("SELECT * FROM [Person] WHERE Id = @Id;", new { Id = 10045});
```

Not to mention, with the new enhanced compiler, the library even become more faster and efficient than it was before.

### Class Handler

The feature [ClassHandler](feature/classhandlers) has been introduced as part of the package. It allows you to handle the event during the serialization/deserialization process of the Model and the DbDataReader object (inbound/outbound).

First, implement a class handler by simply implementing the [IClassHandler](/interface/iclasshandler) interface.

```csharp
public class PersonClassHandler : IClassHandler<Person>
{
    public Person Get(Person entity,
        DbDataReader reader)
    {
        return entity;
    }

    public Person Set(Person entity)
    {
        return entity;
    }
}
```

Then, map the class handler into your model.

```csharp
[ClassHandler(typeof(PersonClassHandler))]
public class Person
{
    ...
}
```

You can also use the [FluentMapper](/mapper/fluentmapper) class if you wish an attribute-free models.

```csharp
FluentMapper
    .Entity<Person>()
    .ClassHandler<PersonClassHandler>();
```

Or, the [ClassHandlerMapper](mapper/classhandlermapper).

```csharp
ClassHandlerMapper
    .Add<Person, PersonClassHandler>(true);
```

After the mappings, when you call any of the push operations (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)), the `Set` method of the property handler will be invoked, thus allowing you to intercept the information prior the actual execution to the database.

On the other hand, when you call any of the push operations (i.e.: [Query](/operation/query), [QueryAll](/operation/queryall), [BatchQuery](/operation/batchquery) and even the raw-SQL based [ExecuteQuery](/operation/executequery)), the `Get` method of the property handler will be invoked for you to verify the extracted information prior returning to the caller.

### Immutable Classes

In the previous versions, an unhandled exception messages are being thrown if the immutable classes are used as the models. However, in order to strengthen the support to the F# programming language, the support to immutable classes has been introduced.

```csharp
public class Person
{
    public Person(int id,
        string name)
    {
        Id = id;
        Name = name;
    }

    public int Id { get; }

    public string Name { get; }
}
```

The name of the constructor arguments must be identical to the name of the properties. Only the matches arguments and properties will be processed during the hydration process. To be safe, ensure that the names of the target arguments are equal to the DbDataReader fields, no matter what.

It also important to take note that you can even combine the read-only fields and the public properties within the model. See below.

```csharp
public class Person
{
    public Person(int id,
        string name)
    {
        Id = id;
        Name = name;
    }

    public int Id { get; }

    public string Name { get; }

    public string Addres { get; set; } // Writable

    public bool IsActive { get; set; } // Writable
}
```

### Anonymous Type / ExpandoObject / IDictionary<string, object>

It is not common for the ORM to support the Anonymous Type Resultset, however in RepoDB we had supported this. This is also a case for use to completely support the F#.

```fsharp
connection.QueryAll<{| Id : int64; Name : string  |}> "Person"
```

Code above is for F#. Below is the C# conversion.

```csharp
// Create a type def
private void Main(string[] args)
{
    var typeDef = new
    {
        Id = default(long),
        Name = default(string)
    };
    var people = Call(typeDef, "Person");
    // Process the 'people' as anonymous
}

private IEnumerable<T> Call<T>(T typeDef,
    string table)
{
    using (var connection = new SqlConnection(connectionString).EnsureOpen())
    {
        return connection.QueryAll<T>(table);)
    }
}
```

Historically, the ExpandoObject and the IDictionary&lt;string, object&gt; is already supported, only if you are using the table-based calls like below.

```csharp
var people = connection.QueryAll("Person");
```

On this release, the calls below will also return the ExpandoObject.

```csharp
var people = connection.QueryAll<dynamic>("Person");
// Or
var people = connection.QueryAll<ExpandoObject>("Person");
```

Or, even inferred as IDictionary&lt;string, object&gt;.

```csharp
var people = connection.QueryAll<IDictionary<string, object>>("Person");
```

### Table-Based Fluent Calls

In the previous version, there is no way for you to reuse the model unless you are working with the dynamics and Anonymous Types. In this release, you can reuse an exact single model to whatever operations you would like.

Let us say created a model person.

```csharp
public class Person
{
    public long Id { get; set; }
    public string Name { get; set; }
}
```

And by doing the code below, it directly query the `Person` table.

```csharp
var people = connection.QueryAll<Person>();
```

Moving forward, you can use the same model for as long it defines the characteristics of the records. Below is the code that queries the `Supplier` and the `Customer` table using the same `Person` model.

```csharp
var customers = connection.QueryAll<Person>("Customer");
var suppliers = connection.QueryAll<Person>("Supplier");
```

The functionality are also extended to all other operations (i.e.: [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update) and etc).

```csharp
var person = new Person
{
    Name = "John Doe"
};
var customerId = connection.Insert<Person>("Customer", person);
var supplierId = connection.Insert<Person>("Supplier", person);
```

The importance of this feature is to allow you to save the repetitive models you are creating when designing an application. The ratio of the cases may not be high, but it is quite important in some cases.

### The 'fields' argument

This new feature is requested by the community to make the library a completely dynamic ORM. Historically, you can only update specific columns if you are using the dynamics and/or Anonymous Types based calls. See below.

```csharp
var id = connection.Insert("Customer", new
{
    Name = "John Doe",
    Address = "New York"
});
```

The call above inserts a record into the `Customer` table targeting only the `Name` and the `Address` columns. Other columns that are not a part of the Anonymous Type you passed will not be part of the execution.

However, in this release, you can do such thing even with the models. Simply specify the list of the fields you wish to be covered by the operation at the `fields` argument.

```csharp
var customer = new Customer
{
    Name = "John Doe",
    Address = "New York",
    IsActive = true,
    CreatedDateUtc = DateTime.UtcNow
};
var fields = Field.Parse<Customer>(e => new
{
    e.Name,
    e.Address
});
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Customer>(customer, fields: fields);
}
```

In which, among all the properties the `Customer` model has, only the `Name` and the `Address` columns are being processed.

You can as well do it in literal string like below.
```csharp
var customer = new Customer
{
    Name = "John Doe",
    Address = "New York",
    IsActive = true,
    CreatedDateUtc = DateTime.UtcNow,
    UpdatedDateUtc = DateTime.UtcNow
};
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Customer>(customer,
        fields: Field.From("Name", "Address"));
}
```

The `fields` argument is now exposed to all the important push and pull operations (i.e.: [Query](/operation/query), [Merge](/operation/merge), [Update](/operation/update) and all the corresponding batch operations).

### Typed Result Execution

A very simple yet quitely important features awaited by some of the users of the library. On this release, the Type Result set is now supported.

Historically, if you wish to query a single column from the table, you are required to return it as dynamics or an explicit class.

```csharp
public class Person
{
    public string Name
}
```

And do the query like below.

```csharp
// ExecuteQuery
var people = connection.ExecuteQuery<Person>("SELECT Name FROM Person;");

// Or even with Fluent
var people = connection.QueryAll<Person>();
```

Moving forward, you can do it like below.

```csharp
// String
var names = connection.ExecuteQuery<string>("SELECT Name FROM Person;");

// Long
var ids = connection.ExecuteQuery<long>("SELECT Id FROM Person;");
```

Without even creating a model, you can directly inferred the result to any .NET CLR type.

You can even do it with the enumerations.

```csharp
public enum Gender
{
    Male,
    Female
}
```

And do the query like below.

```csharp
var gender = connection.ExecuteQuery<Gender>("SELECT Gender FROM Person WHERE Name = @Name;",
     new { Name = "John Doe" });
```

Also, a part of the package with this, if you have a table with single column (which most likely not happening at all), you directly query the records like below.

```csharp
var subjectNames = connection.QueryAll<string>("Subject");
```

Or just force a fluent call for single field.

```csharp
var productNames = connection.QueryAll<string>("Product",
     fields: Field.From("Name"));
```

### Support to Table-Valued Parameters (TVP)

The current release is now fully acknowledging the support to the Table-Valued Parameters (TVP) via User-Defined Types (UDT) from the SQL Server.

To do this, simply create a UDT from the database, and then create a stored procedure that accepts those UDT type. You can follow this [guidelines](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/table-valued-parameters) from Microsoft.

Let us say, you created a stored procedure named `sp_process_people` that accepts the UDT of a name `PersonType`. The stored procedure is merging the received information into the actual table `Person`, then returns all the newly inserted records.

To call such thing, simply create a `DataTable` and set the `TableName` property equals to the name of the UDT.

```csharp
var table = new DataTable();
table.TableName = "[dbo].[PersonType]"; // Name of the UDT
// Create the 'table' columns/rows
```

Then, simply call the stored procedure like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var tables = connection.ExecuteQuery<Person>("EXEC [sp_InsertPerson] @PersonTable = @Table;",
        new { Table = table })?.AsList();
}
```

### Breaking Changes

In this section, we will enumerate some of the know breaking changes.

#### Merge 'qualifiers' and 'fields' argument

If you have called the [Merge](/operation/merge) operation with the list of [Field](/class/field) objects as the second argument and had not specified the argument name during the calls, then that would trigger a compilation error moving foward (see below).

```csharp
var people = GetMergeablePeople();
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var qualifiers = Field.Parse<Person>(e => new
    {
         e.Name,
         e.DateOfBirth
    });
	var mergedRows = connection.Merge<People>(people, qualifiers);
}
```

Historically, the second argument is the `qualifiers`, however, in the current release, the `fields` argument is also placed as the second argument in the overloaded method, therefore invalidating the calls above.

To fix the issue, simply specify the name of the arguments to your call like below.

```csharp
var qualifiers = Field.Parse<Person>(e => new
{
    e.Name,
    e.DateOfBirth
});
var mergedRows = connection.Merge<Person>(people, qualifiers: qualifiers);
```

And even you are using both.

```csharp
var qualifiers = Field.Parse<Person>(e => new
{
    e.Name,
    e.DateOfBirth
});
var fields = Field.Parse<Person>(e => new
{
    e.Id,
    e.Name,
    e.DateOfBirth,
    e.IsActive
});
var mergedRows = connection.Merge<People>(people, qualifiers: qualifiers, fields: fields);
```

#### The 'Where/WhereOrPrimaryKey' vs 'What'

In the previous version, if you had forced using the named argument to `whereOrPrimaryKey` and/or `where` argument when calling any of the push/pull operations, that may be encountering the compiler error.

In the current release, we renamed the `whereOrPrimaryKey` and some of the placements of the `where` argument into `what`. The motivation behind this is, the `what` now supports 3 contexts.

- Entity Model / Type / Anonymous Type
- PrimaryKey
- IdentityKey

Historically, the identity key is not being identified.

So, all the previous calls like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.Query<Person>(whereOrPrimaryKey: 10045);
}
```

Must be refactored to like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.Query<Person>(what: 10045);
}
```

Or, simply eliminate the named argument.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.Query<Person>(10045);
}
```

The update must be done to the other methods that accepts the mentioned staled arguments (i.e.: [Delete](/operation/delete), [Update](/operation/update) and the others).

### Closing Note

The release updates also comes with various requests from the community, bug fixes, enhancements and major refactoring. It is impotant to see the detailed updates from the [Releases](/release/core) page and/or by visiting the actual release page for this version at the Github [releases](https://github.com/mikependon/RepoDB/releases/tag/untagged-eb36fd782c723e532054) page.

We also would like to extend our warmth thanks to all the contributors, the collaborators and the issue reporters.

Please do not forget to share this news to your friends, colleagues and followers. Thank you!
