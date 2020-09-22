---
layout: post
title: "Announcing RepoDB version 1.12.0"
author: "Michael Camara Pendon"
date: 2020-09-21 00:00:00 +0200
categories: blogs repodb
---

Today, we are announcing the availability of [RepoDb v1.12.0](https://www.nuget.org/packages/RepoDb/1.12.0). The released packages also comes with its bundles, the extension libraries at v1.1.0. See below.

- [RepoDb.SqlServer v1.1.0](https://www.nuget.org/packages/RepoDb.SqlServer/1.1.0)
- [RepoDb.SqlServer.BulkOperations v1.1.0](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations/1.1.0)
- [RepoDb.SqLite v1.1.0](https://www.nuget.org/packages/RepoDb.SqLite/1.1.0)
- [RepoDb.MySql v1.1.0](https://www.nuget.org/packages/RepoDb.MySql/1.1.0)
- [RepoDb.MySqlConnector v1.1.0](https://www.nuget.org/packages/RepoDb.MySqlConnector/1.1.0)
- [RepoDb.PostgreSql v1.1.0](https://www.nuget.org/packages/RepoDb.PostgreSql/1.1.0)

### TL;DR;

The version [v1.12.0](https://www.nuget.org/packages/RepoDb/1.12.0) together with its extensions (at [v1.1.0](https://repodb.net/release/core)) releases are one of the biggest suite release of RepoDB. It has the major updates to its Core Features and Capabilities (Enhancements, Bug Fixes and Requests from the .NET Community). It also includes the initial support to F# programming language; bundled by the new Enhanced Compiler, Class Handler and Advanced Dynamic Operation Invocations.

### Highlights

Below are the list of the updates/changes covered by this release.

- [TL;DR;](#tldr)
- [Highlights](#highlights)
- [Initial Support to FSharp](#initial-support-to-fsharp)
- [Enhanced Compiler](#enhanced-compiler)
- [Class Handler](#class-handler)
- [Immutable Classes](#immutable-classes)
- [ExecuteQuery 2nd Layer Cache](#executequery-2nd-layer-cache)
- [Anonymous Type / ExpandoObject / IDictionary<string, object>](#anonymous-type--expandoobject--idictionarystring-object)
- [Table-Based Fluent Calls](#table-based-fluent-calls)
- [The 'fields' argument](#the-fields-argument)
- [Typed Result Execution](#typed-result-execution)
- [Support to Table-Valued Parameters (TVP)](#support-to-table-valued-parameters-tvp)
- [Breaking Changes](#breaking-changes)
  - [The Merge Argument ('qualifiers' vs 'fields')](#the-merge-argument-qualifiers-vs-fields)
  - [The 'Where/WhereOrPrimaryKey' vs 'What'](#the-wherewhereorprimarykey-vs-what)
  - [The DataReader.ToEnumerable Method](#the-datareadertoenumerable-method)
- [Closing Note](#closing-note)

It is important to not skip the [Breaking Changes](#breaking-changes) section.

### Initial Support to FSharp

This release package comes with the initial support to F# programming language together with the various requests from the F# community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being an active collaborators here.

To start with, first, install the package. In this sample, we will use the SQL Server.

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
let connection = (new SqlConnection(url))
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

By default, all the functionalities and features written for C# programming language is also inheritted by F# programming language. It is also important to take note that the `CLIMutable` attribute can be eliminated to all your models starting from this release.

**Disclaimer:** The option types in F# is not supported if it is being placed at the [ClassHandler](/feature/classhandlers) and [PropertyHandler](/feature/propertyhandlers) objects.

### Enhanced Compiler

The core compiler of RepoDB has been rewritten from being a monolithic codebase to a multiple smaller codebases. All the compiler classes needed for the AOT compilations has been splitted into several partial classes. With these updates, the code are much more easy, neat and cleaner.

The reasons and the motivations behind this are the following.

- The old compiler code is harder to maintain; RepoDB itself is becoming more complex. The new compiler is easy, simple, neat and clean. It adheres to the clean-code principles.
- If the Conversion is Automatic, the old compiler does multiple conversion of the expression, thus slowing down the process.
- There are multiple repeatitive code in the old compiler which has been completely eliminated.
- To simplify the error handling. It was also introduced on most critical part of the new compiler, thus give a better error messages to the user of the library.
- Difficulty of the implementation to the extended functionality (i.e.: [ClassHandler](/feature/classhandlers), [PropertyHandler](/feature/propertyhandlers)). It is very easy to do such things with the new compiler.

In addition to this, with the new compiler, the Anonymous Type Param Argument is now being cached and compiled ahead-of-time (AOT), thus making this library more performant and much memory-efficient when calling the underlying Execute methods (i.e.: [ExecuteQuery](/operation/executequery), [ExecuteNonQuery](/operation/executenonquery), [ExecuteScalar](/operation/executescalar), [ExecuteReader](/operation/executereader) and [ExecuteQueryMultiple](/operation/executequerymultiple)). See below.

```csharp
connection.ExecuteQuery<Person>("SELECT * FROM [Person] WHERE Id = @Id;",
    new { Id = 10045});
```

Not to mention, with the new enhanced compiler, the library even become more faster and efficient than it was before.

### Class Handler

The feature [ClassHandler](feature/classhandlers) has been introduced as part of the package. It allows you to handle the event during the serialization/deserialization process of the class model and the DbDataReader object (inbound/outbound).

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

Or, the [ClassHandlerMapper](/mapper/classhandlermapper).

```csharp
ClassHandlerMapper
    .Add<Person, PersonClassHandler>(true);
```

After the mappings, when you call any of the push operations (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)), the `Set` method of the property handler will be invoked, thus allowing you to intercept the information prior the actual execution to the database.

On the other hand, when you call any of the push operations (i.e.: [Query](/operation/query), [QueryAll](/operation/queryall), [BatchQuery](/operation/batchquery) and even the raw-SQL based [ExecuteQuery](/operation/executequery)), the `Get` method of the property handler will be invoked for you to verify the extracted information prior returning to the caller.

### Immutable Classes

In the previous versions, an unhandled exception messages are being thrown if the immutable classes are used as the models. However, in order to strengthen the support to F# programming language, the support to immutable classes has been introduced.

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

The name of the constructor arguments must be identical to the name of the properties. Only the matches arguments and properties will be processed during the hydration process. Therefore, to be safe, ensure that the names of the target arguments are equal to the DbDataReader fields, no matter what.

It also important to take note that you can even combine the constructor arguments together with the public properties within the model. See below.

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

### ExecuteQuery 2nd Layer Cache

Historically, the 2nd Layer cache capability is not introduced in the [ExecuteQuery](/operation/executequery) operation for the purpose of avoiding the collisions. However, since this release has supported the table-based calls for the Entity Model, it has also validated this feature, therefore, starting from this release, the 2nd Layer cache will be supported in this method.

Let us say you have a cache factory named `MemoryCacheFactory` that creates a single instance of [ICache](/interface/icache) object. Then, the code below is now valid.

```csharp
var memoryCache = MemoryCacheFactory.Create();
using (var connection = new SqlConnection(connectionString))
{
    var products = connection.ExecuteQuery<Product>("SELECT * FROM Product;",
        cacheKey: "AllProducts", cache: memoryCache);
}
```

The first call will query the database, the 2nd call will query the data from the supplied cache object. By default, the cache item expires within 180 minutes, but such settings can be changed by simply passing the value in the `cacheItemExpiration` argument during the calls.

```csharp
var products = connection.ExecuteQuery<Product>("SELECT * FROM Product;",
    cacheKey: "AllProducts",
    cache: memoryCache,
    cacheItemExpiration: 300 /* 5 hours */);
```

If you are working with repository (i.e.: [BaseRepository](/class/baserepository) and [DbRepository](/class/dbrepository)), you do not need to pass the instance of the [ICache](/interface/icache) object. It is handled by the repository internally through [MemoryCache](/class/memorycache) object.

```csharp
using (var repository = new DbRepository(connectionString))
{
    var products = repository.ExecuteQuery<Product>("SELECT * FROM Product;",
        cacheKey: "AllProducts");
}
```

Fluent Calls Collisions: For as long you are pointing to the same [ICache](/interface/icache) object, you can collide the information with the fluent calls.

The call below is valid colliding with the cache item specified on the execute method above.

```csharp
var memoryCache = MemoryCacheFactory.Create();
using (var connection = new SqlConnection(connectionString))
{
    var products = connection.QueryAll<Product>(cacheKey: "AllProducts",
        cache: memoryCache);
}
```

Of course, if the type of the cached items are not equal to the target result type, then an exception will be thrown. See below.

```csharp
var memoryCache = MemoryCacheFactory.Create();
using (var connection = new SqlConnection(connectionString))
{
    var suppliers = connection.QueryAll<Supplier>(cacheKey: "AllProducts",
        cache: memoryCache);
}
```

### Anonymous Type / ExpandoObject / IDictionary<string, object>

It is not common for the ORM to support the Anonymous Type Resultset, however in RepoDB we had supported this. This is also a case for this library to completely support the F# programming language.

```fsharp
connection.QueryAll<{| Id : int64; Name : string  |}> "Person"
```

The code snippet above is for F# programming language, and below is the equivalent C# programming language.

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
    using (var connection = new SqlConnection(connectionString))
    {
        return connection.QueryAll<T>(table);)
    }
}
```

Historically, both the ExpandoObject and the IDictionary&lt;string, object&gt; objects are already supported as the query resultset when fetching the data from the database, but only if you are using the table-based calls like below.

```csharp
var people = connection.QueryAll("Person");
```

On this release, the calls below will also return the ExpandoObject.

```csharp
var people = connection.QueryAll<dynamic>("Person");
// Or
var people = connection.QueryAll<ExpandoObject>("Person");
```

Or, you can even inferred as IDictionary&lt;string, object&gt;.

```csharp
var people = connection.QueryAll<IDictionary<string, object>>("Person");
```

And below is the equivalent for F# programming language.

```fsharp
let people = connection.QueryAll<IDictionary<string, obj>> "Person";
```

It is also important that this feature is not limited to the fluent calls. You can also use it in the actual [ExecuteQuery](/operation/executequery) operation.

```csharp
var sql = "SELECT * FROM Person;";
var people = connection.ExecuteQuery<IDictionary<string, object>>(sql);
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

The importance of this feature is to allow you to eliminate the repetitive models you are creating when designing an application. The ratio of the cases may not be high, but it is quite important in some cases.

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

However, in this release, you can do such thing even with the actual class models. Simply specify the list of the fields you wish to be covered by the operation at the `fields` argument.

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

In which, among all the properties of the `Customer` class model has, only the `Name` and the `Address` columns are being processed.

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

Historically, if you wish to query a single column from the table, you are required to return it as dynamics or an explicit class model.

Via a dynamic object of type `ExpandoObject`.

```csharp
// Returns an IEnumerable<ExpandoObject>
var people = connection.ExecuteQuery("SELECT Name FROM Person;");
```

Or, with a class model with single property.

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

// Fluent QueryAll
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
var sql = "SELECT Gender FROM Person WHERE Name = @Name;";
var gender = connection.ExecuteQuery<Gender>(sql, new { Name = "John Doe" });
```

Also, as a part of the this package release, if you have a table with single column (which most likely not happening at all), you can directly query the records like below.

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

Let us say, you created a stored procedure named `sp_InsertPerson` that accepts the UDT of a name `PersonType`. The stored procedure is merging the received information into the actual table `Person`, then returns all the newly inserted records.

To call such thing, simply create a `DataTable` and set the `TableName` property equals to the name of the UDT.

```csharp
var table = new DataTable();
table.TableName = "[dbo].[PersonType]"; // Name of the UDT
// Create the 'table' columns/rows
```

Then, simply call the stored procedure like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var sql = "EXEC [sp_InsertPerson] @PersonTable = @Table;";
    var people = connection.ExecuteQuery<Person>(sql, new { Table = table });
}
```

### Breaking Changes

In this section, we will enumerate some of the known breaking changes from the previous releases of this library. It is important for you to spend time reading this.

#### The Merge Argument ('qualifiers' vs 'fields')

If you had called the [Merge](/operation/merge) operation with the list of [Field](/class/field) objects as the second argument and had not specified the argument name during the calls, then that would trigger a compilation error moving foward (see below).

```csharp
var people = GetMergeablePeople();
using (var connection = new SqlConnection(connectionString))
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

And if calling for the fields.

```csharp
var fields = Field.Parse<Person>(e => new
{
    e.Id,
    e.Name,
    e.DateOfBirth,
    e.IsActive
});
var mergedRows = connection.Merge<Person>(people, fields: fields);
```

And if you are using both.

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
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.Query<Person>(whereOrPrimaryKey: 10045);
}
```

Must be refactored to like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.Query<Person>(what: 10045);
}
```

Or, simply eliminate the named argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.Query<Person>(10045);
}
```

The update must be done to the other methods that accepts the mentioned staled arguments (i.e.: [Delete](/operation/delete), [Update](/operation/update) and the others).

#### The DataReader.ToEnumerable Method

The method that is used to extract the DbDataReader object into a target .NET CLR Type has been completely modified. In the previous version, the method named `ToEnumerable` with a signature like below is present, together with its corresponding `async` method.

```csharp
public IEnumerable<TResult> ToEnumerable<TResult>(DbDataReader reader,
    IDbConnection connection = null,
    IDbTransaction transaction = null);
```

However, in the new version, the method signature has been completely modified with the one below.

```csharp
ToEnumerable<TResult>(DbDataReader reader,
    IEnumerable<DbField> dbFields = null,
    IDbSetting dbSetting = null)         
```

Also, the `async` method has been removed as it is not necessary anymore.

The rationale behind this are the following.

- Eliminate the DB calls from the compiler.
- Support the Enhanced Compiler scenarios.
- Simplify the calls of the possible users.

### Closing Note

The release updates also comes with various requests from the community, bug fixes, enhancements and major refactoring. It is important to see the detailed updates from the [Releases](/release/core) page and/or by visiting the actual release page for this version at the Github [releases](https://github.com/mikependon/RepoDB/releases/tag/untagged-eb36fd782c723e532054) page.

We also would like to extend our warmth thanks to all the contributors, the collaborators and the issue reporters.

Please do not forget to share this news to your friends, colleagues and followers. Thank you!
