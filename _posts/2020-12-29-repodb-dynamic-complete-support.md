---
layout: post
title: "Announcing RepoDB version 1.12.5"
author: "Michael Camara Pendon"
date: 2020-12-30 06:00:00 +0200
categories: blogs repodb
---

Today, we are announcing the availability of [RepoDb v1.12.5](https://www.nuget.org/packages/RepoDb/1.12.5) and its bundles, the extension libraries at &gt;= v1.1.2. See below.

- [RepoDb.SqlServer v1.1.2](https://www.nuget.org/packages/RepoDb.SqlServer/1.1.2)
- [RepoDb.SqlServer.BulkOperations v1.1.2](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations/1.1.2)
- [RepoDb.SqLite v1.1.2](https://www.nuget.org/packages/RepoDb.SqLite/1.1.2)
- [RepoDb.MySql v1.1.3](https://www.nuget.org/packages/RepoDb.MySql/1.1.3)
- [RepoDb.MySqlConnector v1.1.2](https://www.nuget.org/packages/RepoDb.MySqlConnector/1.1.2)
- [RepoDb.PostgreSql v1.1.2](https://www.nuget.org/packages/RepoDb.PostgreSql/1.1.2)

### TL;DR;

These releases comes with the complete support to the DYNAMIC operations. It also comes with a lot of bug fixes and enhancements.

> The major release feature is being called DYNAMICS as it is enabling the dynamic capabilities if we are to use it on the context of ORM. Therefore, do not get confused comparing the terminology against the dynamics within .NET ecosystem.

### Push Operations

This release is now supporting the capability of pusing an instance of `ExpandoObject` or `IDictionary<string, object>` object into the push operations mentioned below.

- [Insert](/operation/insert)
- [Merge](/operation/merge)
- [Update](/operation/update)

And an instance of `IEnumerable<ExpandoObject>` or `IEnumerable<IDictionary<string, object>>` object into the push operations mentioned below.

- [InsertAll](/operation/insertall)
- [MergeAll](/operation/mergeall)
- [UpdateAll](/operation/updateall)
- [BulkDelete](/operation/bulkdelete)
- [BulkInsert](/operation/bulkinsert)
- [BulkMerge](/operation/bulkmerge)
- [BulkUpdate](/operation/bulkupdate)

Though the use-case ratio percentage is low, but at some cases (edge-cases), it is quitely important to get enabled.

The main target is just to fully enable the support to the dynamic-capabilities in the context of an ORM.

#### Sample Scenarios

Let us say, you would like to copy the data from one table into another table that is residing on a different database, then the dynamic code snippet below would do the job for you.

```csharp
using (var sourceConnection = new SqlConnection(sourceConnection))
{
    var people = sourceConnection.QueryAll("[dbo].[Person]");
    using (var destinationConnection = new SqlConnection(destinationConnection))
    {
        destinationConnection.InsertAll("[sales].[Customer]", people);
    }   
}
```

When you query all the people from the `[dbo].[Person]` table, it returns an instance of `IEnumerable<ExpandoObject>`. The [InsertAll](/operation/insertall) is now capable of accepting such argument value.

If you think the data is quite big enough, you can instead use the [BulkInsert](/operation/bulkinsert) operation like below.

```csharp
destinationConnection.BulkInsert("[sales].[Customer]", people);
```

Also, notice that you have not even created a single model to accomplish the job, for as long the structure of the source table `[dbo].[Person]` is identical to the destination table `[sales].[Customer]`, then you can do insert and/or bulk-process it.

You can as well filter the results based on the target fields from the destination table like below.

```csharp
using (var sourceConnection = new SqlConnection(sourceConnection))
{
    using (var destinationConnection = new SqlConnection(destinationConnection))
    {
        var fields = DbFieldCache
            .Get("[sales].[Customer]", destinationConnection, transaction: null)
            .Select(e => e.AsField());
        var people = sourceConnection.QueryAll("[dbo].[Person]", fields: fields);
        destinationConnection.InsertAll("[sales].[Customer]", people);
    }   
}
```

Or, just add your own literal string like below.

```csharp
using (var sourceConnection = new SqlConnection(sourceConnection))
{
    using (var destinationConnection = new SqlConnection(destinationConnection))
    {
        var fields = Field.From("Name", "Address", "SSN");
        var people = sourceConnection.QueryAll("[dbo].[Person]", fields: fields);
        destinationConnection.InsertAll("[sales].[Customer]", people);
    }   
}
```

> As the `ExpandoObject` is also a dictionary object underneath, if the identity column is present at the destination table but is absent on the source list (i.e.: `IEnumerable<ExpandoObject>`, `IEnumerable<IDictionary<string, object>>`) during the operations (i.e.: [Insert](/operation/insert), [InsertAll](/operation/insertall) and [BulkInsert](/operation/bulkinsert)), then, the new key-value-pair (KVP) entry will be created on the underlying dictionary object. The same logic is implied to the [Merge](/operation/merge), [MergeAll](/operation/mergeall) and [BulkMerge](/operation/bulkmerge) operations.

### Operations

Below are the code snippets for each of the affected operation.

#### Insert

Below is a sample code snippet for [Insert](/operation/insert) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var person = new Dictionary<string, object>
    {
        { "Name", "John Doe" },
        { "Address", "New York" }
    };
    var id = connection.Insert("[dbo].[Person]", person);
}
```

> If the identity column is present on the `[dbo].[Person]` table, then a new entry of key-value-pair will be added to the `person` variable.

#### InsertAll/BulkInsert

Let us say you have this method.

```csharp
public IEnumerable<IDictionary<string, object>> GetPeople(int count = 10)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Dictionary<string, object>
        {
            { "Name", $"Name {i}" },
            { "Address", $"Address{i}" }
        };
    }
}
```

Then, below is a sample code snippet for [InsertAll](/operation/insertall) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var people = GetPeople(100);
    var insertedRows = connection.InsertAll("[dbo].[Person]", people);
}
```

And below is for [BulkInsert](/operation/bulkinsert) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var people = GetPeople(10000);
    var insertedRows = connection.BulkInsert("[dbo].[Person]", people);
}
```

> If the identity column is present on the `[dbo].[Person]` table, then the new entries of the key-value-pairs will be added to each item of the `people` variable.

#### Merge

Below is a sample code snippet for [Merge](/operation/merge) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var person = new Dictionary<string, object>
    {
        { "Id", 10045 }
        { "Name", "James Smith" },
        { "Address", "New York" }
    };
    var id = connection.Merge("[dbo].[Person]", person);
}
```

> If the identity column is present on the `[dbo].[Person]` table, then a new entry of key-value-pair will be added to the `person` variable.

By default, the primary and/or identity column will be used as qualifier, but you can specify your custom qualifiers like below.

```csharp
var id = connection.Merge("[dbo].[Person]", people, qualifiers: Field.From("Name"));
```

#### MergeAll/BulkMerge

Let us say you have this method.

```csharp
public IEnumerable<IDictionary<string, object>> GetPeople(int count = 10)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Dictionary<string, object>
        {
            { "Id", i }
            { "Name", $"Name {i} - Merged" },
            { "Address", $"Address{i} - Merged" }
        };
    }
}
```

Then, below is a sample code snippet for [MergeAll](/operation/mergeall) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var people = GetPeople(100);
    var mergedRows = connection.MergeAll("[dbo].[Person]", people);
}
```

And below is for [BulkMerge](/operation/bulkmerge) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var people = GetPeople(10000);
    var mergedRows = connection.BulkMerge("[dbo].[Person]", people);
}
```

> If the identity column is present on the `[dbo].[Person]` table, then the new entries of the key-value-pairs will be added to each item of the `people` variable.

By default, the primary and/or identity column will be used as qualifier, but you can specify your custom qualifiers like below.

```csharp
// MergeAll
var mergedRows = connection.MergeAll("[dbo].[Person]",
    people,
    qualifiers: Field.From("Name"));

// BulkMerge
var mergedRows = connection.BulkMerge("[dbo].[Person]",
    people,
    qualifiers: Field.From("Name"));
```

#### Update

Below is a sample code snippet for [Update](/operation/update) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var person = new Dictionary<string, object>
    {
        { "Id", 10045 },
        { "Name", "John Doe" },
        { "Address", "California" },
        { "UpdatedUtc", DateTime.UtcNow }
    };
    var updatedRows = connection.Update("[dbo].[Person]", person);
}
```

By default, the primary and/or identity column will be used as qualifier, but you can specify your custom qualifiers like below.

```csharp
var updatedRows = connection.Update("[dbo].[Person]", people, qualifiers: Field.From("Name"));
```

#### UpdateAll/BulkUpdate

Let us say you have this method.

```csharp
public IEnumerable<IDictionary<string, object>> GetPeople(int count = 10)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Dictionary<string, object>
        {
            { "Id", i }
            { "Name", $"Name {i} - Updated" },
            { "Address", $"Address{i} - Updated" }
        };
    }
}
```

Then, below is a sample code snippet for [Updated](/operation/updateall) operation.


```csharp
using (var connection = new SqlConnection(connection))
{
    var people = GetPeople(100);
    var mergedRows = connection.UpdateAll("[dbo].[Person]", people);
}
```

And below is for [BulkUpdate](/operation/bulkupdate) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var people = GetPeople(10000);
    var updatedRows = connection.BulkUpdate("[dbo].[Person]", people);
}
```

By default, the primary and/or identity column will be used as qualifier, but you can specify your custom qualifiers like below.

```csharp
// UpdateAll
var updatedRows = connection.UpdateAll("[dbo].[Person]",
    people,
    qualifiers: Field.From("Name"));

// BulkUpdate
var updatedRows = connection.BulkUpdate("[dbo].[Person]",
    people,
    qualifiers: Field.From("Name"));
```

#### BulkDelete

Let us say you have this method.

```csharp
public IEnumerable<IDictionary<string, object>> GetKeys(int count = 10)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Dictionary<string, object>
        {
            { "Id", i },
            { "Name", $"Name {i}" }
        };
    }
}
```

And below is for [BulkDelete](/operation/bulkdelete) operation.

```csharp
using (var connection = new SqlConnection(connection))
{
    var people = GetPeople(10000);
    var deletedRows = connection.BulkDelete("[dbo].[Person]", people);
}
```

By default, the primary and/or identity column will be used as qualifier, but you can specify your custom qualifiers like below.

```csharp
var deletedRows = connection.BulkDelete("[dbo].[Person]", people, qualifiers: Field.From("Name"));
```

Or, you can also just bulk-delete through the list of primary keys.

```csharp
var keys = new [] { 10045, ..., 11211 };
using (var connection = new SqlConnection(connection))
{
    var deletedRows = connection.BulkDelete("[dbo].[Person]", primaryKeys: keys);
}
```

### Bug Fixes and Enhancements

- Adhoc: Remove the Initialize method from the [DataEntityDataReader](/class/dataentitydatareader). [#673](https://github.com/mikependon/RepoDB/issues/673) - this is a breaking changes
- Bug: Query failed when field mapping used on FSharp records. [#662](https://github.com/mikependon/RepoDB/issues/662)
- Bug: [FluentMapper](/mapper/fluentmapper) is not working with Abstract Properties [#666](https://github.com/mikependon/RepoDB/issues/666)
- Bug: [BulkInsert](/operation/bulkinsert) with mappings [#668](https://github.com/mikependon/RepoDB/issues/668)
- Question: [IPropertyHandler](/interface/ipropertyhandler) for `Dictionary<string, string>` [#647](https://github.com/mikependon/RepoDB/issues/647)
- Upgraded the `Microsoft.Data.SqlClient` to `v2.1.0`.
- Upgraded the `System.ComponentModel.Annotations` to `v5.0.0`.
- Request: Introduce optional caching to [ExecuteScalar](/operation/executescalar) methods. [#648](https://github.com/mikependon/RepoDB/issues/648)
- Enhancement: Add the non-parameterized Truncate operation. [#652](https://github.com/mikependon/RepoDB/issues/652)
- Request: Do not crash if on empty enumerable on bulk operation [#635](https://github.com/mikependon/RepoDB/issues/635).
- Bug: Exception is being thrown for the Type level [PropertyHandler](/features/propertyhandlers) for Dynamic Insertion [#628](https://github.com/mikependon/RepoDB/issues/628).
- Added a validation to ensure the type of the `TPropertyHandler` has implemented the [IPropertyHandler](/interface/ipropertyhandler) interface when calling the `Add()` method of the [PropertyHandlerCache](/cacher/propertyhandlercache) object.
- Enhancement: Add the dynamic [ExecuteQueryMultiple](/operation/executequerymultiple) method in the [DbRepository](/class/dbrepository) [#630](https://github.com/mikependon/RepoDB/issues/630)
- Possible bug - Enum with Flag attribute is not correctly handled [#624](https://github.com/mikependon/RepoDB/issues/624)
- [Core] Support `ExpandoObject` in [Insert](/operation/insert) via `TableName`. [#234](https://github.com/mikependon/RepoDB/issues/234)
- [Core] Support `ExpandoObject` in [Merge](/operation/merge) via `TableName`. [#238](https://github.com/mikependon/RepoDB/issues/238)
- [Core] Support `ExpandoObject` in [Update](/operation/update) via `TableName`. [#236](https://github.com/mikependon/RepoDB/issues/236)
- [Core] Support `ExpandoObject` in [InsertAll](/operation/insertall) via `TableName`. [#235](https://github.com/mikependon/RepoDB/issues/235)
- [Core] Support `ExpandoObject` in [MergeAll](/operation/mergeall) via `TableName`. [#239](https://github.com/mikependon/RepoDB/issues/239)
- [Core] Support `ExpandoObject` in [UpdateAll](/operation/updateall) via `TableName`. [#237](https://github.com/mikependon/RepoDB/issues/237)
- Introduce the support of Dynamic and ExpandoObject in [BulkInsert](/operation/bulkinsert). [#243](https://github.com/mikependon/RepoDB/issues/243)
- Enhancement: Support `ExpandoObject` in [BulkInsert](/operation/bulkinsert) [#610](https://github.com/mikependon/RepoDB/issues/610)
- Enhancement: Support `ExpandoObject` in [BulkMerge](/operation/bulkmerge) [#611](https://github.com/mikependon/RepoDB/issues/611)
- Enhancement: Support `ExpandoObject` in [BulkUpdate](/operation/bulkupdate) [#612](https://github.com/mikependon/RepoDB/issues/612)
- Enhancement: Support `ExpandoObject` in [BulkDelete](/operation/bulkdelete) [#613](https://github.com/mikependon/RepoDB/issues/613)
- Bug: BulkInsert with mappings [#668](https://github.com/mikependon/RepoDB/issues/668)
- Bug: System.Data.SqlClient.SqlException: 'Column name 'ID' does not exist in the target table or view.' [#651](https://github.com/mikependon/RepoDB/issues/651)
- Refactor the internal method implementations to only utilize the base methods for both the MDS and SDS method calls.
- Bug: Incorrect syntax near the keyword `WITH` [BulkMergeAsync](/operation/bulkinsert) [#640](https://github.com/mikependon/RepoDB/issues/640)
- Request: Do not crash if on empty enumerable on bulk operation [#635](https://github.com/mikependon/RepoDB/issues/635)

### Credits

Thank you to our contributors and to all the collaborators.

#### Contributors

- [fredliex](https://github.com/fredliex) - for issuing the following PRs
  - [#657](https://github.com/mikependon/RepoDB/issues/657) - fix [#650](https://github.com/mikependon/RepoDB/issues/650) `TypeExtension.IsPlainType` seems to misjudge the string property
  - [#658](https://github.com/mikependon/RepoDB/issues/658) - some unit test case assign culture en-US when convert `DateTime` to `String`.
  - [#680](https://github.com/mikependon/RepoDB/issues/680) - fix convert error for type level with `propertyHandler`. 🚀 **This is awesome!**
- [SergerGood](https://github.com/SergerGood) - for issuing the following PRs. Btw, thanks for being a frequent PR contributor.
  - [#625](https://github.com/mikependon/RepoDB/issues/625) - Benchmarks for the UpdateAll methods
  - [#620](https://github.com/mikependon/RepoDB/issues/620) - Benchmarks for the GetAll methods
- [Swoorup](https://github.com/Swoorup) - for issuing a PR [#619](https://github.com/mikependon/RepoDB/issues/619)
- [SpaceOgre](https://github.com/SpaceOgre) - for issuing a PR [#678](https://github.com/mikependon/RepoDB/issues/678)
- [stefandevo](https://github.com/stefandevo) - for issuing a PR [#642](https://github.com/mikependon/RepoDB/issues/642) - Bug: Incorrect syntax near the keyword 'WITH' BulkMergeAsync
- [bobduncan](https://github.com/bobduncan) - for issuing a PR [#649](https://github.com/mikependon/RepoDB/issues/649) - [#645](https://github.com/mikependon/RepoDB/issues/645) Correctly passed through tableName parameter in the DeleteAll 

#### Collaborators

For our active collaborators on this release:

- [Swoorup](https://github.com/Swoorup)
- [fredliex](https://github.com/fredliex)
- [SpaceOgre](https://github.com/SpaceOgre)
- [kbilsted](https://github.com/kbilsted)
- [Jmaharman](https://github.com/Jmaharman)
- [nandaccio](https://github.com/nandaccio)
- [stefandevo](https://github.com/stefandevo)
- [bobduncan](https://github.com/bobduncan)

For the other collaborators of this release (filing the Issues/Bugs, Questions, Recommendations, Verification/Validations, etc)

- [rledyard](https://github.com/rledyard)
- [lucwuyts](https://github.com/lucwuyts)
- [gkn06](https://github.com/gkn06)
- [Olli64](https://github.com/Olli64)
- [BieleckiLtd](https://github.com/BieleckiLtd)
- [cwsterling](https://github.com/cwsterling)
- [DmitryBatalov](https://github.com/DmitryBatalov)

Please continue to do so and many thanks!