---
layout: page
title: "QueryGroup (RepoDb)"
permalink: /class/querygroup
tags: [repodb, class, querygroup, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## QueryGroup

This class is used to group the list of fields used in the query expressions. It usually refers to a group of fields at the `WHERE` statement of the SQL statement.

It contains the list of child Query Group(s) and [QueryField](/class/queryfield)(s), [Conjunction](/enumeration/conjunction) and the unary expression of `NOT`.

By using this class, it would increase the performance of your application as the library's core implementation is very dependent on the tree structuring of the query objects.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Creating an Instance

Below is the way on how to create an instance of this class.

```csharp
var field = new QueryGroup(new QueryField("Id", 10045));
```

Or, by passing an array of [QueryField](/class/queryfield) objects.

```csharp
var queryFields = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(queryFields);
```

Also, you can pass a child query group to further deepen the tree expressions.

```csharp
var whereA = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var whereB = new []
{
    new QueryField("LastName", Operation.NoLike, "Doe%"),
    new QueryField("State", Operation.NotEqual, "Michigan"),
    new QueryField("Age", Operation.NotBetween, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(new [] { new QueryGroup(whereA), new QueryGroup(whereB) });
```

#### Creating through Parse

You can also create a query group by parsing any object (.NET CLR Types or Dynamic).

```csharp
var param = new // This is a dynamic, can also be .NET CLR Type
{
    LastName = "Doe",
    State = "Michigan"
};
var queryGroup = QueryGroup.Parse(param);
```

> Using the above parse method, all the parsed fields will always have the `Equal` operation.

Or, via expression.

```csharp
var queryGroup = QueryGroup.Parse<Person>(p => p.LastName == "Doe" && State == "Michigan");
```

#### Setting the Conjunction

By default, the conjuction is equals to `AND`. Let us say you have the codes below.

```csharp
var queryFields = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(queryFields);
```

The SQL statement expression will be generated as follows.

```csharp
WHERE ([LastName] LIKE @LastName AND [State] = @State AND [Age] BETWEEN (@Age_1, @Age_2));
```

You can change it by passing the conjunction value to `OR`.

```csharp
var queryFields = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(queryFields, Conjunction.Or);
```

And the SQL statement will be generated as below.

```csharp
> WHERE ([LastName] LIKE @LastName OR [State] = @State OR [Age] BETWEEN (@Age_1, @Age_2));
```

#### Unary IS NOT

There is a scenario where we are negating the condition of the expressions by simply reversing the logic.

This is being solved by this `IsNot` property.

Let us say, you would to query all the people records that is not-active and not-male.

```csharp
var queryFields = new []
{
    new QueryField("IsActive", true),
    new QueryField("Gender", "Male")
};
var queryGroup = new QueryGroup(queryFields);
```

The statement above will generate a SQL below.

```csharp
> WHERE ([IsActive] = @IsActive OR [Gender] = @Gender);
```

To negate, simply pass the value of `True` in the `isNot` constructor argument.

```csharp
var queryGroup = new QueryGroup(queryFields, true);
```

Then the statement will be generated as below.

```csharp
> WHERE NOT ([IsActive] = @IsActive OR [Gender] = @Gender);
```

> By default, the value is `False`. Please be reminded that negating does not gives you the most performant condition when writing SQL. It still recommended to create a targetted query expression rather than negating it.

#### Getting all the Children

To retrieve the child Query Group(s), use the `QueryGroups` property.

```csharp
var whereA = new QueryField("FirstName", Operation.Like, "J%");
var whereB = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var whereC = new []
{
    new QueryField("LastName", Operation.NoLike, "Doe%"),
    new QueryField("State", Operation.NotEqual, "Michigan"),
    new QueryField("Age", Operation.NotBetween, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(whereA, new [] { new QueryGroup(whereB), new QueryGroup(whereC) });

// Access via `QueryGroups` property
var queryGroups = queryGroup.QueryGroups;

// There will be 2 QueryGroup(s) at the 'queryGroups' variable
```

To retrieve the child [QueryField](/class/queryfield)(s), use the `QueryFields` property.

```csharp
var queryFields = queryGroup.QueryFiels;
// There will be 1 QueryField at the 'queryFields' variable
```

However, you can use the `GetFields()` method to get all traversed [QueryField](/class/queryfield) objects.

```csharp
var queryFields = queryGroup.GetFields(true);
// There will be 7 QueryField(s) at the 'queryFields' variable
```

#### Converting to an Enumerable

You can call the `AsEnumerable()` method to convert the instance of this class to an `IEnumerable<QueryGroup>` object.

```csharp
var queryField = new QueryField("CreatedDateUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1));
var queryGroup = new QueryGroup(queryField).AsEnumerable();
```

#### Use-Cases

We recommend you to visit the [use-cases](/class/queryfield#use-cases) section of the [QueryField](/class/queryfield) class.

#### Retrieving the Conjunction Text

To retrieve the text of the [Conjunction](/operation/conjunction), simply call the `GetConjunctionText()` method.

```csharp
var where = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.NBetween, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(where);
var conjunction = queryGroup.GetConjunctionText();
```

The value of the `conjunction` variable would be `AND`.

#### The Fix Method

This method is used within the library core's implementation when it comes to the execution of the actual operation.

The purpose of this method is to fix the possible collision of the parameter names.

Let us say you have created this expression.

```csharp
var whereA = new QueryField("FirstName", Operation.Like, "J%");
var whereB = new QueryField("FirstName", Operation.Like, "A%");
var whereC = new QueryField("FirstName", Operation.Like, "G%");
var queryGroup = new QueryGroup(new [] { whereA, whereB, whereC });
```

The supposed generated SQL text will be below.

```csharp
> WHERE (FirstName = @FirstName AND FirstName = @FirstName AND FirstName = @FirstName);
```

The SQL statement may not caused any problem when it comes to execution, but that is wrong when it comes to parameter-passing.

By calling the `Fix()` method, this collision will be fixed along the way.

```csharp
queryGroup.Fix();
```

And the SQL statement will be generated as below.

```csharp
> WHERE (FirstName = @FirstName AND FirstName = @FirstName_1 AND FirstName = @FirstName_2);
```

#### The GetString Method

This method is used to get the string representation of the QueryGroup object into a SQL statement. This is used internally by the library during the creation of the SQL statements.

```csharp
var where = new[]
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.NotBetween, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(where);
var whereText = queryGroup.GetString(connection.GetDbSetting());
```

The value of the `whereText` variable would be like below.

```csharp
([LastName] LIKE @LastName AND [State] = @State AND [Age] NOT BETWEEN @Age_Left AND @Age_Right)
```

#### Reusability

We sometimes have a scenario to reuse the instance of this class just to avoid creating the same expression.

To reuse the instance, simply call the `Reset()` method.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var queryFields = new []
    {
        new QueryField("LastName", Operation.Like, "Doe%"),
        new QueryField("State", Operation.Equal, "Michigan"),
        new QueryField("Age", Operation.Between, new [] { 20, 40 })
    };
    var where = new QueryGroup(queryFields);
    var people = connection.Query<Person>(where);
    
    // Do the stuffs for 'people' here

    // Reset here
    where.Reset();

    // Reuse it here
    var customers = connection.Query<Customer>(where);

    // Do the stuffs for 'customers' here
}
```

> Above calls was happened at the `IEnumerable<QueryGroup>` object. You can also call the `Reset()` method on an instance basis.
