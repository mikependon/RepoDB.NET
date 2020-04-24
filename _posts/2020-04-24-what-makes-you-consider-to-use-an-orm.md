---
layout: post
title: "Practical Reason why choose an ORM over raw ADO.NET"
author: "Michael Camara Pendon"
date: 2020-04-23 5:30:00 +0100
categories: blogs repodb
---

The extent of this post is generally targetting the whole ORM families running in various technology stack. But I will limit the explanation to only .NET (`C#` or `VB`) and will write the samples in `C#`.

I am the author of `RepoDb` and most samples could be specifics to this ORM, but again, the reasoning is beyond the technology stacks and libraries you preferred to work with. Think of the samples as a code for your favorite ORM (ie: `Entity Framework`, `NHibernate`, `SQLAlchemy`, `ActiveRecord`, `LLBLGen` or `Dapper`).

Below are the reasons why you should choose an ORM over writing a raw SQL yourself using ADO.NET.

#### Write-Less Do-More

By today, we mostly develop micro-components to sustain business demands in a fast-changing industry (via `On-Premise` or `Cloud`, `Windows` or `Linux`, etc).

When using an ORM, there is no doubt that you write less-codes that does the same functionalities as you can write with raw ADO.NET. You also deliver more working codes in a very short amount of time by using it.

Somebody may claims that he/she can write more features using ADO.NET than by using an ORM.

Well, then you have to help them realize how many times their fingers stroke the keyboard just provide the same functionalities the ORMs can.

*A good programmer who wrote good codes via raw implementations can provide better results using an ORM.*

The codes below are very-squeezed raw implementation that you need to write just to query the contents of a single table.

```csharp
var customers = new List<Customer>();
using (var connection = new SqlConnection(ConnectionString))
{
    connection.Open();
    using (var command = connection.CreateCommand())
    {
        command.CommandText = "SELECT [Id], [Name] FROM [dbo].[Customer];";
        using (var reader = new command.ExecuteReader())
        {
            while (reader.Read())
            {
                customers.Add(new Customer
                {
                    Id = reader.GetInt32(0),
                    Name = reader.GetString(1)
                });
            }
        }
    }
}
// Process the `customers` here
```

> Not to mention the validations that you need to write on top (ie: columns nullability, conversions, etc).

In ORM, you just have to write the codes below.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var customers = connection.QueryAll<Customer>();
    // Process the `customers` here
}
```

We are comparing `20` lines of codes against `4` lines of codes. You saved `75%` of your efforts! By using an intelligent IDE via auto-completion (and dot-notation), you may had saved `95%` of your efforts!

Also, imagine you would like to bring multiple datasets into the database with some considerations of `deletes`, `updates` and `merges` operation. In raw ADO.NET, that will take time to implement and maintain.

One can argue that they can place this to the base repository objects so it is reusable. Well, imagine the time you spent writing such base implementations and the code-complexities you are going to maintain moving forward.

#### ORM is Intelligent

Most ORM understands your database schema. When you call the ORM operation (any operation) for the first time, it does pre-touches to your database to retrieve the necessay information from your database concerning to the requested type. The information retrieved is usally the structure of the database table that is equivalent to the requested type.

With the information the ORM has retrieved from your database, it can do create the best compilation it can provide concerning to performance and efficiency.

In ADO.NET, when you are writing a code to retrieve the records, you have to always consider whether the column is nullable or not. And by doing this, we ended up writing some helper class to simplify the implementation.

```csharp
var people = new List<People>();
using (var connection = new SqlConnection(ConnectionString))
{
    connection.Open();
    using (var command = connection.CreateCommand())
    {
        command.CommandText = sqlText;
        using (var reader = new command.ExecuteReader())
        {
            while (reader.Read())
            {
                people.Add(new Person
                {
                    Id = reader.GetInt32(0),
                    Name = ToData<string>(reader, 1),
                    DateOfBirth = ToData<DateTime>(reader, 2),
                    Address = ToData<String>(reader, 3),
                    CreatedDateUtc = ToData<DateTime>(reader, 4)
                });
            }
        }
    }
}
```

And below is a very simple helper method.

```csharp
private T ToData<T>(DbDataReader reader,
    int index)
{
    return reader.IsDbNull(index) == false ? (T)reader[index] : default(T);
}
```

Well, TBH, there is no problem on the code above except for the following.

- The `IsDbNull` check is slow if you place that in every property.
- The calls to method indexer is much slower than the method `Get<Type>`.

When using an ORM, such implementation is already considered internally. And since the ORM understands your schema and knows that some of your column is of specific type and whether it is nullable or not, then the ORM is skipping such checks and is uses the correct `Get<Type>` method to extract the results.

Below are some of the considerations the ORM has considered that is mostly unseen by the developers.

- Iterations between table column count vs class property count
- Type checks, it tries to avoid conversion if necesarry
- Indexer calls vs the `Get<Type>` calls
- Skipping the nullability check
- Reusability of the already extracted objects (taken from cache)

#### Ahead of Time-Compilation

When you write your own code (as a developer), you mostly do not care about the pre-compilation techniques, what you only care is to make your code run fast and efficient (in your own way). Of course, I cannot generalize this to all developers, but this is the common case.

**So what behinds it?** Well, it matters to the real concern of `performance` and `efficiency`.

Most codes are binaries and logical expressions. If it is not `1` then it is `0`. Same goes with the compilers, with AOT compilation, the codes that you are trying to execute is pre-compiled and that brings you to the shortest path possible to attain the results.

Below is a very simple explanation concerning the compilation path.

###### Normal Compilation

Let us say, you would like to write something based on the given `int` value.

```csharp
private void WriteName(int value)
{
    if (value == 1)
    {
        Console.WriteLine("Plant");
    }
    else if (value == 2)
    {
        Console.WriteLine("Animal");
    }
    else if (value == 3)
    {
        Console.WriteLine("Human");
    }
    else
    {
        Console.WriteLine("We do not know");
    }
}
```

The method `WriteName()` is provided by you and is part of the runtime compilation. Then, let us say you call the method like below.

```csharp
WriteName(3); // Human
```

When you call the `WriteName()` method, the codes inside this method will be executed `1-by-1`. A hit to first `if` is considered 1 hit, a hit to second `if` is considered the 2nd hit and so on.

The output of literal text `Human` is written after hitting `3` lines of code. This hits will happen everytime you call the `WriteName()` method and if you're passing the value of `3`. In short, if you do iterate `1000` times, then you actually hits `3000` line of codes.

Well, you can further improve this way when you do a manual pre-compilation of the `WriteName()` method ahead of time. You can create a targetted compiled-expression to specifically write the output in the first hit. The process of compiling this code is to generate a compiled-IL (or Expression) that accepts the same argument but is only doing the logic specifics to that purpose.

###### Pre-Compilation Pseudo-Code

The pseudo-code below checks whether the value's expression is present in the cache, if not, it then creates a compiled expression for the requested value and cache it.

```csharp
PUBLIC VOID GetExpression(VALUE)
BEGIN
    CHECK MEMORY CACHE

    LET expression = CHECK MEMORY CACHE
    
    IF (expression is NULL) THEN
        BEGIN
        IF (VALUE is 1) THEN
        BEGIN
            SET expression = COMPILE FOR PLANT
        END
        ELSE IF (VALUE is 2) THEN
        BEGIN
            SET expression = COMPILE FOR ANIMAL
        END
        ELSE IF (VALUE is 3) THEN
        BEGIN
            SET expression = COMPILE FOR HUMAN
        END
        ELSE
        BEGIN
            SET expression = COMPILE FOR SOMETHING
        END
    END

    CACHE expression INTO MEMORY
END
```

Then, the way you call such method is to invoke the compiled expression.

```csharp
var humanExpression = GetExpression(3);

// In which you can utilize this as many as you can
expression.Invoke();
```

With this, when you iterate `1000` times, you only hit `1000` lines of codes. Well, TBH, it is a bit tricky!

In anyway, such implementations are considered by ORM and is not being considered by the developers during the development. Well, it is just crazy if the developer will write such consideration on their project.

###### ORM Compilation

Let us say you have this table.

```csharp
CREATE TABLE [dbo].[Person]
(
    Id INT IDENTITY NOT NULL
    , Name NVARCHAR(256) NOT NULL
    , DateOfBirth DATETIME2(5) NOT NULL
    , Address NVARCHAR(MAX) NULL
    , CreatedDateUtc DATETIME2(5) NOT NULL
    , CONSTRAINT [CRIX_Person_Id] PRIMARY KEY CLUSTERED ([Id] ASC) ON [PRIMARY]
);
```

And you have this class.

```csharp
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string Address { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

Then you call the ORM operations as follows.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var people = connection.QueryAll<Person>();
    // Process the `people` here
}
```

The ORM has created a compiled-IL (or expression) specific to type `Person`. That compiled expression function is then reused by that ORM everytime there is an invocation that matters with type `Person`.

If you however call the ORM operation as follows.

```csharp
using (var connection = new SqlConnection(ConnectionString))
{
    var people = connection.ExecuteQuery<Person>("SELECT Id, Name FROM Person;"); // Targetting only 2 columns
    // Process the `people` here
}
```

The ORM is creating a seperate compiled-IL (or expression) for the type `Person` concerning on the requested properties. In this case, the ORM can provide maximum performance when manipulating the objects.

#### Performance

Data Reader Indexer vs GetType and NullChecks

#### Complexity


#### Maintainability

#### Quality

Most ORMs are well-tested and is being used by thousand of developers. The community itself are helping the ORM authors to stabilize and improve the library. The way it happens is through an open collaboration via various channels (ie: GitHub, StackOverflow, GitterChat, etc) and discussing specific things (features) to be employed, in the end, the requestor itself or the contributor does the validation and testing (in actual real-world scenarios).

The collaborations is beyond on the things that you usually get with your own circles, and it is free if you're using an open-source ORM.

The qualities can be measured in a different way like number of Tests (Unit/Integration), number of Users, number of running applications in Production, etc. When writing your own implementation, you have to spend time writing such Test Suites and convince multiple users to use and test your code before even usable for actual Production environment. Otherwise, one can rely in a low-quality written codes with a risks of error post-production.