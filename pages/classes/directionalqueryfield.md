---
layout: default
sidebar: classes
title: "QueryField"
description: "A class that is used to define a query expression for the SQL statement that is bi-directional."
permalink: /class/directionalqueryfield
tags: [repodb, class, directionalqueryfield, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: CLASSES
---

# DirectionalQueryField

---

This is used as a field on the query expression that supports two direction (input/output) via `ParamDirection` enumeration of the `System.Data` namespace. It inherits and works like the [QueryField](/class/queryfield) class, however, it was extended to be capable of receiving an output data from the database after the execution.

### Use-Cases

This can be very useful if you would like to execute and retrieve an output from an existing stored procedure from the database.

Let us say, you have the stored procedure below.

```
CREATE PROCEDURE [dbo].[sp_Multiply]
(
    @Value1 DOUBLE
    , @Value2 DOUBLE
    , @Output DOUBLE OUT
)
AS
BEGIN
    SET @Output = @Value1 * @Value2;
END
```

Then, you can call it like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var output = new DirectionalQueryField("Output", typeof(double), ParamDirection.Output);
    var param = new []
    {
        new QueryField("Value1", 100),
        new QueryField("Value2", 200),
        output
    };
    var result = connection.ExecuteNonQuery("sp_Multiply", param, commandType: CommandType.StoredProcedure);
    // Use the 'output' here like below
    var value = output.GetValue<double>(); // Or, you can also access the parameter itself like `output.Parameter.Value`
}
```

Notice, in the second parameter, you pass an instance of .NET CLR Type, this define the type of the parameter from the database.

If you are passing a value to the input/output parameters, you can simply pass the value in the overloaded constructor. By this time, you are not required to pass the type of the parameter as the library will identify the parameter type based from the passed value.

```csharp
var output = new DirectionalQueryField("Output", "Whatever", ParamDirection.InputOutput); // Type is TEXT
var output = new DirectionalQueryField("Output", 100, ParamDirection.InputOutput); // Type is INT
```

> If you are calling the [ExecuteReader](/operation/executereader) operation, please be noted that the output parameter is only set once the instance of the `DbDataReader` is disposed. This is a default behaviour of the ADO.NET.

For more information, please visit its base object [QueryField](/class/queryfield).