---
layout: navpage
sidebar: references
title: "Query Expressions Reference (RepoDb)"
permalink: /reference/queryexpressions
tags: [repodb, class, queryexpressions, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Query Expressions

This page contains the recommended way of using the query expressions.

#### Disclaimer

The support to query objects are massive and well tested with high-quality. However, the Linq-Expression parser of the library is not as extensive as Entity Framework. Therefore, we highly recommend to always use the [QueryGroup](/class/querygroup) and [QueryField](/class/queryfield) objects when composing a complex expressions.

#### Equal

Via Dynamic:

```csharp
var result = connection.Query<Customer>(new { Id = 10045});
```

> The query via dynamic object is only supporting the `Equal` operation.

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Id == 10045);
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.Equal, 10045));
```

#### NotEqual

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Name != "Anna Fullerton");
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotEqual, "Anna Fullerton" });
```

#### LessThan

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Id < 100);
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.LessThan, 100 });
```

#### GreaterThan

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Id > 100);
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.GreaterThan, 100 });
```

#### LessThanOrEqual

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Id <= 100);
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.LessThanOrEqual, 100 });
```

#### GreaterThanOrEqual

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Id >= 0);
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.GreaterThanOrEqual, 0 });
```

#### Like

There are various combinations.

##### Contains

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Name.Contains("Anna"));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.Like, "%Anna%" });
```

##### StartsWith

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Name.StartsWith("Anna"));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.Like, "Anna%" });
```

##### EndsWith

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Name.EndsWith("Anna"));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.Like, "%Anna" });
```

#### NotLike

There are various combinations.

##### Contains

Via Expression:

```csharp
var result = connection.Query<Customer>(e => !e.Name.Contains("Anna"));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotLike, "%Anna%" });
```

##### StartsWith

Via Expression:

```csharp
var result = connection.Query<Customer>(e => !e.Name.StartsWith("Anna"));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotLike, "Anna%" });
```

##### EndsWith

Via Expression:

```csharp
var result = connection.Query<Customer>(e => !e.Name.EndsWith("Anna"));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotLike, "%Anna" });
```

#### Between

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Id >= 10045 && e.Id <= 10075));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.Between, new [] { 10045, 10075 } ));
```

#### NotBetween

Via Expression:

```csharp
var result = connection.Query<Customer>(e => e.Id <= 10045 && e.Id >= 10075));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.NotBetween, new [] { 10045, 10075 } ));
```

#### In

Via Expression:

```csharp
var keys = new[] { 10045, 10046, 10047, 10048 };
var result = connection.Query<Customer>(e => keys.Contains(e.Id)));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.In, new [] { 10045, 10046, 10047, 10048 }));
```

#### NotIn

Via Expression:

```csharp
var keys = new[] { 10045, 10046, 10047, 10048 };
var result = connection.Query<Customer>(e => !keys.Contains(e.Id)));
```

Via [QueryField](/class/queryfield).

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.NotIn, new [] { 10045, 10046, 10047, 10048 }));
```

#### Complex Expressions

As mentioned in the [disclaimer](#disclaimer) above, do not to do this.

```csharp
var result = connection.Query<Customer>(e => (e.IsActive == true && (e.DateInserted >= Yesterday && e.DateInserted <= Today) && (new[] { "Washington", "New York", "California" }).Contains(e.State)) ||
    (e.IsActive == false && (e.DateInserted >= LastMonth && e.DateInserted <= Yesterday) && (new[] { "Washington", "New York", "California" }).Contains(e.State));
```

> We will support the complex Linq-expression soon. But, until further notice, please use the query objects when composing complex expressions.

Instead, do this.

```csharp
// Left Or
var whereActive = new QueryField("IsActive", true);
var whereDate = new QueryField("DateInserted", Operation.Between, new [] { Yesterday, Today });
var whereState = new QueryField("State", Operation.In, new [] { "Washington", "New York", "California" });

// Right Or
var whereActiveRight = new QueryField("IsActive", false);
var whereDateRight = new QueryField("DateInserted", Operation.Between, new [] { LastMonth, Yesterday });
var whereStateRight = new QueryField("State", Operation.In, new [] { "Washington", "New York", "California" });

// Expression
var leftGrouping = new QueryGroup(new [] { whereActive, whereDate, whereState });
var rightGrouping = new QueryGroup(new [] { whereActiveRight, whereDateRight, whereStateRight });
var where = new QueryGroup(new [] { leftGrouping, rightGrouping }, Conjunction.Or);

// Query
var result = connection.Query<Customer>(where));
```

Will generate the SQL below.

```csharp
> WHERE (([IsActive] = @IsActive1 AND [DateInserted] BETWEEN @Date1 AND @Date2 AND [State] IN (@State1, @State2, @State3) OR ([IsActive] = @IsActive2 AND [DateInserted] BETWEEN @Date3 AND @Date4 AND [State] IN (@State4, @State5, @State6))
```