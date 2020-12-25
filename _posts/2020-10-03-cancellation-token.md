---
layout: post
title: "RepoDB's Complete Support to CancellationToken"
author: "Michael Camara Pendon"
date: 2020-10-03 14:00:00 +0200
categories: blogs repodb
---

# RepoDB's Complete Support to CancellationToken

---

Today, we are announcing the complete support of [RepoDB](/) to the [CancellationToken](https://docs.microsoft.com/en-us/dotnet/api/system.threading.cancellationtoken?view=netcore-3.1).

Starting at version [v1.12.4](https://www.nuget.org/packages/RepoDb/1.12.4) and its corresponding extended libraries for different DB Providers at version [1.1.1](https://www.nuget.org/packages/RepoDb.SqlServer/1.1.1), all the `Async` methods within the library will be having an additional argument `cancellationToken` (defaulted to `CancellationToken.None` and/or `default`).

### How will it affect you?

When you upgrade, you can leverage the possibility of cancellable async operations. All the existing calls you made to any operations will not be affected (both compilation/signatures and the behaviors).

See the sample calls below for [ExecuteNonQuery](/operation/executenonquery).

```csharp
var tokenSource = new CancellationTokenSource();
using (var connection = new SqlConnection(connectionString))
{
    var result = await connection.ExecuteQueryAsync<DateTime>("SELECT GETUTCDATE();",
        cancellationToken: tokenSource.Token);
    // You can cancel the token anytime
}
```

And for the [Query](/operation/query) operation.

```csharp
var tokenSource = new CancellationTokenSource();
using (var connection = new SqlConnection(connectionString))
{
    var people = await connection.QueryAllAsync<Person>(cancellationToken: tokenSource.Token);
    // You can cancel the token anytime
}
```

And for the [Insert](/operation/insert) operation.

```csharp
var tokenSource = new CancellationTokenSource();
using (var connection = new SqlConnection(connectionString))
{
    var id = await connection.InsertAsync<Person, long>(new
    {
        Name = "John Doe",
        Address = "New York"
    },
    cancellationToken: tokenSource.Token);
    // You can cancel the token anytime
}
```

Such calls will be identical to all other `async` methods/operations within the library.

### Breaking Changes

There is one breaking change that will be covered by this release. But the use-case to this is very minimal and/or almost 0.

If you have created your own customized [IDbHelper](/interface/idbhelper) object, then, you need to change your signature by simply adding the `cancellationToken` argument in both methods defined on this interface.

#### GetFieldsAsync

```csharp
public async Task<IEnumerable<DbField>> GetFieldsAsync(IDbConnection connection,
    string tableName,
    IDbTransaction transaction = null,
    CancellationToken cancellationToken = default)
```

#### GetScopeIdentityAsync

```csharp
public async Task<object> GetScopeIdentityAsync(IDbConnection connection,
    IDbTransaction transaction = null,
    CancellationToken cancellationToken = default)
```

### Closing Note

You can visit the actual [release](https://github.com/mikependon/RepoDB/releases/tag/v1.12.4) from our Github page.