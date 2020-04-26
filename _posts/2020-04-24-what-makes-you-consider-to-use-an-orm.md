---
layout: post
title: "Practical Reason why choose an ORM over raw ADO.NET"
author: "Michael Camara Pendon"
date: 2020-04-25 5:55:00 +0200
categories: blogs repodb
---

The extent of this post is generally targetting the whole ORM families running in various technology stacks. But I will limit the explanation to only .NET (`C#` or `VB`) and will write the samples in `C#`.

I am the author of `RepoDb` and most samples could be specifics to this ORM, but again, the rationale of this post is beyond the technology stacks and libraries you preferred to work with. Think of all the samples as a code for your favorite ORM (ie: `Entity Framework`, `NHibernate`, `LLBLGen` or `Dapper`).

After reading this article, you may realize some technicalities you will consider when writing a raw implementations. In that point in time, you can certainly beat the ORM in all cases. But until to the fact that you are not writing the perfect codes (or putting extra time and effort into it), then using the raw implementation could not ever beat the ORM in all cases (ie: `Performance`, `Efficiency`, `Maintainability` and `Simplicity`).

> The content written is based on the actual experiences when developing an ORM, and also, a collective information provided by different authors of different ORMs.

Anyway, below are the low-level technical reasons why you should choose an ORM over writing a raw SQL yourself using ADO.NET.

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

We are comparing `20` lines of codes against `4` lines of codes. You had saved `75%` of your efforts, and by using an intelligent IDE with auto-completion (via `Resharper` or whatever), you may be saved the additional `95%` of your efforts!

Also, imagine that you would like to bring a multiple datasets into the database with some considerations of `deletes`, `updates` and `merges` operation. In raw ADO.NET, that will take time to implement and maintain.

One can argue that they can place this to the base repository object so it is reusable. Well, imagine the time you spent writing such base implementations and the code-complexities you are going to maintain moving forward.

#### ORM is Intelligent

Most ORM understands your database schema. When you call the ORM operation (any operation) for the first time, it does pre-touches to your database to retrieve the necessary information from your database (concerning to the requested type). The information retrieved is usally the structure of the database table that is equivalent to the requested type.

With the information the ORM has retrieved from your database, it can do create the best compilation it can provide concerning to performance and efficiency.

In ADO.NET, when you are writing a code to retrieve the records, you have to always consider whether the column is `nullable` or not. And by doing this, we ended up writing some helper class to simplify the implementation.

```csharp
var people = new List<People>();
using (var connection = new SqlConnection(ConnectionString))
{
    connection.Open();
    using (var command = connection.CreateCommand())
    {
        command.Connection = connection;
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

**Well, TBH, the problem on the codes above are the following.**

- The `IsDbNull` check is slow if you place that in every property.
- The calls to method indexer is much slower than the method `Get<Type>()`.

When using an ORM, such implementation is already considered internally. And since the ORM understands your schema and knows that some of your column is of specific type and whether it is `nullable` or not, then the ORM is skipping such checks and is uses the correct `Get<Type>()` method to extract the results.

**Below are some of the considerations the ORM has considered that is mostly unseen by the developers.**

- Iterations between table column count vs class property count
- Type checks, it tries to avoid conversion if necesarry
- Indexer calls vs the `Get<Type>()` calls
- Skipping the nullability check
- Reusability of the already extracted objects (taken from cache)

#### Ahead of Time-Compilation

When you write your own code (as a developer), you mostly do not care about the pre-compilation techniques, what you only care is to make your code run fast and efficient (in your own way). Of course, I cannot generalize this to all developers, but this is the common case.

**So what behinds it?** Well, it matters to the real concern of `performance` and `efficiency`.

Most codes are binaries and logical expressions. If it is not `1` then it is `0`. Same goes with the compilers, with AOT compilation, the codes that you are trying to execute is pre-compiled and that brings you to the shortest path possible to attain the results.

Below is a very simple explanation concerning the compilation path.

###### Normal Runtime Compilation

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

The output of literal text `Human` is written after hitting `3` lines of code. These hits will happen everytime you call the `WriteName()` method and if you're passing the value of `3`. In short, if you do iterate `1000` times, then you actually hits `3000` line of codes.

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

The ORM is creating a seperate compiled-IL (or expression) for the type `Person` concerning on the requested properties. It automatically projects the difference of the request based on the requested properties. In this case, the ORM can provide maximum performance when manipulating the objects.

#### Performance

Concerning to performance, one can say that no-one can beat the raw implementation. I certainly agree to this `100%`, but you have to realize that in order for your to maximize the performance, you have to write the codes perfectly.

We (as a developer) oftenly forgotten (or never consider) some low-level technicalities about which specific raw methods we can use to maximize the performance.

**To reflect my claims in the reality, we as a developer mostly do the following.**

- Used the `int` or `name` indexer when extracting a result from the data reader.
- Converting the value to a correct type (why not `try-cast` first?).
- Creating a shared helper method for `DbNull` check.
- Used a reflection to call the necessary `Get<Type>()` methods.
- Oftenly used an `atomic` operation over `batch/bulk` operations.
- Converting the data entities into `DataTable` before bulk-inserting.

###### Int/Name Indexer vs `Get<Type>` Methods

As a developer, we often write the codes below.

```csharp
while (reader.Read())
{
    var entity = new Entity
    {
        Id = Convert.ToInt32(reader["Id"]),
        Name = Convert.ToString(reader["Name"]),
        CreatedDateUtc = Convert.ToDateTime(reader["CreatedDateUtc"]),
        ...
    };
}
```

Over the codes below.

```csharp
while (reader.Read())
{
    var entity = new Entity
    {
        Id = reader.GetInt(0),
        Name = reader.GetString(1),
        CreatedDateUtc = reader.GetDateTime(2),
        ...
    };
}
```

The reason why we used the `int` and `name` indexer is code-readability and index mapping positions. So even we changed the position of our columns in query text, the codes will still work.

In the underlying implementation of ADO.NET, the `int` and `name` indexer is first finding the position of the column by name (ie: `GetOrdinal` method), then returning a value of `System.Object`. Once you retrieved the value (as `System.Object`), you are obligued to cast or convert it back to a target .NET CLR type (based on class property type).

The reason why the `Get<Type>()` method is much more faster is because you issued the index position by default and had defined the target .NET CLR type concerning to that specific position. The position mapping is handled by ORM internally in the pre-compilation.

One can alternatively write the code below, but there is still additional code lines being executed.

```csharp
while (reader.Read())
{
    var entity = new Entity
    {
        Id = reader.GetInt(reader.GetOrdinal("Id")),
        Name = reader.GetString(reader.GetOrdinal("Name")),
        CreatedDateUtc = reader.GetDateTime(reader.GetOrdinal("CreatedDateUtc")),
        ...
    };
}
```

> Nowadays, the performance hit is very negligible as the RAM and Cores are quitely cheap when compared to a decade ago.

###### Type Check - Cast/Boxed vs Conversion

We often forgot to try casting it first (boxing) before converting. Definitely, boxing is way much more faster than conversion. Conversion is an additional layer in between the object and the target .NET CLR type, which means an additional logic has been added.

You can visit the Microsoft documentation [here](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/types/casting-and-type-conversions) or a post from StackOverflow [here](https://stackoverflow.com/questions/638496/what-is-fastest-int-convert-toint32x-or-int32-parsex).

As a developer, we usually write the codes below.

```csharp
while (reader.Read())
{
    var entity = new Entity
    {
        Id = (int)reader["Id"], // or Convert.ToInt32(reader["Id"]) - for safe conversion
        Name = (string)reader["Name"], // Convert.ToString(reader["Name"]) - for safe conversion
        CreatedDateUtc = (DateTime)reader["CreatedDateUtc"], // or Convert.ToDateTime(reader["CreatedDateUtc"]) - for safe conversion
        ...
    };
}
```

Over the codes below.

```csharp
while (reader.Read())
{
    var entity = new Entity
    {
        Id = reader.GetInt(0),
        Name = reader.GetString(1),
        CreatedDateUtc = reader.GetDateTime(2)
        ...
    };
}
```

or the codes below.

```csharp
while (reader.Read())
{
    var entity = new Entity();
    
    var id = reader["Id"];
    entity.Id = id is int ? (int)id : Convert.ToInt32(id);

    var name = reader["Name"];
    entity.Name = name is string ? (string)name : Convert.ToString(name);

    var createdDateUtc = reader["CreatedDateUtc"];
    entity.CreatedDateUtc = createdDateUtc is DateTime ? (DateTime)name : Convert.ToDateTime(createdDateUtc);
}
```

And that being said, the `Get<Type>()` method is the most fastest way of extracting the result.

###### Skipping DBNull Checks

Calling the `IsDbNull()` method makes your extraction slower. As a developer, **do you really skipping null checks?** If yes, then this section is not for you, otherwise please read to realize it.

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

I would expect you often write a helper class to cater the simplicity in your code, like below.

```csharp
var people = new List<People>();
using (var connection = new SqlConnection(ConnectionString))
{
    connection.Open();
    using (var command = connection.CreateCommand())
    {
        command.Connection = connection;
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

Well, as mentioned in the [ORM is Intelligent](#orm-is-intelligent) section, most of the ORM understand your schemas and it tries to skip this null check.

When you call the fetch operation of ORM, it tries to create a compiled-IL (or expression) that is equivalent to the codes below.

```csharp
var people = new List<People>();
using (var connection = new SqlConnection(ConnectionString))
{
    connection.Open();
    using (var command = connection.CreateCommand())
    {
        command.Connection = connection;
        command.CommandText = sqlText;
        using (var reader = new command.ExecuteReader())
        {
            while (reader.Read())
            {
                people.Add(new Person
                {
                    Id = reader.GetInt32(0), // We skip the NULL checks
                    Name = reader.GetString(1), // We skip the NULL checks
                    DateOfBirth = reader.GetDateTime(2), // We skip the NULL checks
                    Address = reader.IsDbNull(3) == false ? reader.GetString(3) : default(string),
                    CreatedDateUtc = reader.GetDateTime(4) // We skip the NULL checks
                });
            }
        }
    }
}
```

###### Consideration of the Batch and Bulk Operations

**How do you insert multiple rows in the database?** Let us say, the responsibility of your application is to replicate a data from the source DB into a destination DB and the frequency is to insert atleast `100` rows for every 5 seconds.

By using `SqlBulkCopy` to just only insert few hundred records is not advisable (though it is a separate debate). Otherwise, you are are committing on the CONS of using the bulk operations (ie: keep locking the table itself, etc).

Well, one can say *I am iterating it!* or somebody can say *We are creating UDT types!*. Both scenario are correct as this is the underlying capability of ADO.NET. But in some cases, iteration seems to be slow and using a UDT type requires a special implementations in both client application and the database.

When you are using an ORM, the batch operations are considered as you are using it.

Let us give you a sample, you wrote the codes below to insert `100` customers using raw implementation.

```csharp
public void InsertCustomers(IEnumerable<Customer> customers)
{
    using (var connection = new SqlConnection(ConnectionString))
    {
        connection.Open();
        using (var transaction = connection.BeginTransaction())
        {
            using (var command = connection.CreateCommand())
            {
                command.Connection = connection;
                command.CommandText = "INSERT INTO [dbo].[Customer] (Name, Address) VALUES (@Name, @Address); SELECT SCOPE_IDENTITY();";

                // Name
                var parameter = command.CreateParameter();
                parameter.ParameterName = "@Name";
                command.Parameters.Add(parameter);
                
                // Address
                parameter = command.CreateParameter();
                parameter.ParameterName = "@Address";
                command.Parameters.Add(parameter);
                
                // Iterate
                foreach (var customer in customers)
                {
                    command.Parameters["@Name"].Value = customer.Name;
                    command.Parameters["@Address"].Value = customer.Address;
                    customer.Id = (int)command.ExecuteScalar();                    
                }
            }
            transaction.Commit();
        }
    }
}
```

Being said, iterating a list of data entities and do execute in every iteration seems to be affecting the performance. Though it is not really performant, but it is doing the job.

Yes, using UDT would solve the problem in the performance, but imagine the time you spent on creating such implementations and maintaining the object in the database and the data conversion in the client. I would humbly say that, most of the developers are using the `reflection` just to convert the `IEnumerable<Person>` into a `DataTable`, and by doing so, it agains falls into a different performance flaws.

By using an ORM, the batch execution is handled. All you have to do is just to call the proper batch method.

```csharp
public void InsertCustomers(IEnumerable<Customer> customers)
{
    using (var connection = new SqlConnection(ConnectionString))
    {
        connection.InsertAll<Customer>(customers); // Does the batch operation
    }
}
```

**What happened behind the scene?**

When you call the batch operations, the ORM creates a multiple statements (packed-statements) and accepts the batches of parameters and execute it all in one-go.

If you insert `100` customers with a batch of `30`, then it only executed `4` times against the database.

- First iteration has insterted the first batch of `30` rows
- Second iteration has insterted the second batch of `30` rows
- Third iteration has insterted the third batch of `30` rows
- Fourth iteration has insterted the last `10` rows

On other hand, a bulk-operation is pre-supported by some ORMs and that could simply help you simplify your implementations.

```csharp
public void InsertCustomers(IEnumerable<Customer> customers)
{
    using (var connection = new SqlConnection(ConnectionString))
    {
        if (customers.Count() >= 1000)
        {
            connection.BulkInsert<Customer>(customers);
        }
        else
        {
            connection.InsertAll<Customer>(customers);
        }
    }
}
```

###### Reflection vs Compilation

As a developer, we are all lazy and would like to simplify everything. We usually write codes that is pluggable and reusable. In the case of data access, what we always do is to create a base implementation (ie: `Repository`) that will contain the CRUD operations.

```csharp
public class RepositoryBase<T> where T : class
{
    public T Get(int id)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            connection.Open();
            using (var command = connection.CreateCommand())
            {
                command.CommandText = $"SELECT * FROM {typeof(T).Name} WHERE Id = @Id";
                command.Connection = connection;
                var parameter = command.CreateParameter();
                parameter.Name = "@Id";
                parameter.Value = id;
                using (var reader = command.ExecuteReader())
                {
                    if (reader.HasRows)
                    {
                        // Do again a reflection to map the data reader into a class
                    }
                }
            }
        }
        return default(T);
    }

    public int Delete(T entity)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            connection.Open();
            using (var command = connection.CreateCommand())
            {
                command.CommandText = $"DELETE FROM {typeof(T).Name} WHERE Id = @Id";
                command.Connection = connection;
                var parameter = command.CreateParameter();
                parameter.Name = "@Id";
                parameter.Value = typeof(T).GetProperty("Id").GetValue(entity);
                return command.ExecuteNonQuery();
            }
        }
    }

    public int Insert(T entity)
    {
        ..
    }

    public int Update(T entity)
    {
        ..
    }
}
```

Just to mention, the code above is expecting a pre-defined property named `Id` as the primary key. If you have other entities that has different primary keys, then you have to handle it separately.

**Well, TBH, there is no way in the world that the code above can beat the ORM when it comes to performance!**

It is using the evil `reflection` in which it significantly affect the type manipulation of your application. When you run the codes and you process multi-thousand rows, you will notice the memory consumptions out of it.

> I will definitely call you the craziest and dumbiest developer if you will spend time writing your own compiler just for this sake (within your application). Why not just install an ORM, it only takes 5 seconds.

**By using an ORM, all possibilities are considered during the pre-compilation.**

- Different primary key name
- Identity property handlers
- Mapping to the actual object in the DB
- Table column vs class property iterations
- Casting and conversion
- Etc

With ORM, you can do the same by simply calling the target operation. All pre-compilation will be done for you.

```csharp
public class RepositoryBase<T> where T : class
{
    public int Delete(T entity)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            return connection.Delete<T>(entity);
        }
    }

    public void Add(T entity)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            return connection.Insert<T>(entity);
        }
    }

    public void Update(T entity)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            return connection.Update<T>(entity);
        }
    }
    
    public T Get(int id)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            return connection.Query<T>(id).FirstOrDefault();
        }
    }
}
```

Imagine the difference and the benefits you are getting here.

#### Maintainabilities

One of the main reason why most developers opt to use an ORM is the maintainability of the codes. When you use an ORM, the codes that you are written is relatively small, thus making it more faster, more easy to read and maintain.

**Imagine the efforts and complexities when creating a repository like this** without you concerning the other possibilities (ie: different `primary keys`, `identities`, generic `column projections`, etc) and the qualities.

```csharp
public interface IRepositoryBase<T> where T : class
{
    T Get(int id);
    int Delete(T entity);
    int Insert(T entity);
    int Update(T entity)
}

public class RepositoryBase<T> : IRepositoryBase<T> where T : class
{
    public T Get(int id)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            connection.Open();
            using (var command = connection.CreateCommand())
            {
                command.CommandText = $"SELECT * FROM {typeof(T).Name} WHERE Id = @Id";
                command.Connection = connection;

                // Id parameter
                var parameter = command.CreateParameter();
                parameter.Name = "@Id";
                parameter.Value = id;

                // Extraction
                using (var reader = command.ExecuteReader())
                {
                    if (reader.HasRows)
                    {
                        // Do again a reflection to map the data reader into a class
                    }
                }
            }
        }
        return default(T);
    }

    public int Delete(T entity)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            connection.Open();
            using (var command = connection.CreateCommand())
            {
                command.CommandText = $"DELETE FROM {typeof(T).Name} WHERE Id = @Id";
                command.Connection = connection;

                // Id parameter
                var parameter = command.CreateParameter();
                parameter.Name = "@Id";
                parameter.Value = typeof(T).GetProperty("Id").GetValue(entity);

                // Execution
                return command.ExecuteNonQuery();
            }
        }
    }

    public int Insert(T entity)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            connection.Open();
            using (var command = connection.CreateCommand())
            {
                command.CommandText = $"INSERT INTO {typeof(T).Name} (...) VALUES (...); SELECT SCOPE_IDENTITY();";
                command.Connection = connection;
                
                // Parameters
                ...

                // Execution
                return (int)command.ExecuteScalar();
            }
        }
    }

    public int Update(T entity)
    {
        using (var connection = new SqlConnection(ConnectionString))
        {
            connection.Open();
            using (var command = connection.CreateCommand())
            {
                command.CommandText = $"UPDATE {typeof(T).Name} SET ... WHERE Id = @Id";
                command.Connection = connection;

                // Id parameter
                var parameter = command.CreateParameter();
                parameter.Name = "@Id";
                parameter.Value = id;
                
                // Other parameters
                ...
                
                // Execution
                return command.ExecuteNonQuery();
            }
        }
    }
}
```

**Than this repository** in which all the possibilities are considered with high-quality and is tested by the community.

```csharp
public interface IRepositoryBase<T> where T : class
{
    T Get(int id);
    int Delete(T entity);
    int Insert(T entity);
    int Update(T entity)
}

public class RepositoryBase<T> : BaseRepository<T, SqlConnection>, IRepositoryBase<T> where T : class
{
    public int Delete(T entity)
    {
        return base.Delete(entity);
    }

    public void Add(T entity)
    {
        return Insert<T>(entity);
    }

    public void Update(T entity)
    {
        return base.Update<T>(entity);
    }
    
    public T Get(int id)
    {
        return Query<T>(id).FirstOrDefault();
    }
}
```

Atleast, I personally preferred that the object (ie: `Repository`) have single responsibility and lesser-code possible for maintainability purposes. **That's being said!**

###### OSS Responsibility

Another reason, if you encountered a problem or issue in the ORM, you are giving that responsibility to the developers of that ORM. You do not have to spend time fixing it, the author (or the community) itself will fix it for you.

**Somebody says I have an issue and I reported it and still not being fixed for a month now!**

That is correct or wrong, as it is all up to you as a developer to choose which ORM are you gonna use for your development. Try to see the number of activities, collaborators, issues and statistics of that ORM before using it. Most author of OSS would like to have a collaboration on the project and that they and the community itself could come up with the collective ideas on how to fix the problems without affecting the other users.

#### Quality

Most ORMs are well-tested and is being used by thousand of developers. The community itself are helping the ORM authors to stabilize and improve the library. The way it happens is through an open collaboration via various channels (ie: `GitHub`, `StackOverflow`, `GitterChat`, etc) and discussing specific things (features) to be employed, in the end, the requestor itself or the contributor does the validation and testing (in actual real-world scenarios).

The collaborations is beyond on the things that you usually get with your own circles, and it is free if you're using an open-source ORM.

The qualities can be measured in a different way like number of Tests (`Unit`/`Integration`), number of Users, number of running applications in Production, etc.

However, when writing your own implementation, you have to spend time writing such Test Suites and convince multiple users to use and test your code before even usable for actual Production environment. Otherwise, one can rely in a low-quality written codes with a risks of error post-production.

------

I will not give a conclusion this post. In anyway, <strong>I thanked you for reading this article.</strong>