---
layout: default
sidebar: classes
title: "DirectionalQueryField"
description: "A class that is being used to define a query expression for the SQL statement that is bi-directional."
permalink: /class/directionalqueryfield
tags: [repodb, directionalqueryfield]
parent: CLASSES
---

# DirectionalQueryField

---

A query field that supports bidirectional parameter passing (input/output) via the `ParamDirection` enumeration in `System.Data`. It extends [QueryField](/class/queryfield) with the ability to receive output values from the database after execution.

This is useful for calling stored procedures that return output parameters.

Given the following stored procedure:

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

Call it as follows:

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

The second constructor argument specifies the .NET CLR type of the database parameter. For input/output parameters, pass the value directly — the library infers the type from the value.

```csharp
var output = new DirectionalQueryField("Output", "Whatever", 256, ParamDirection.InputOutput); // Type is TEXT
var output = new DirectionalQueryField("Output", 100, ParamDirection.InputOutput); // Type is INT
```

{: .note }
> When using [ExecuteReader](/operation/executereader), output parameter values are only populated after the [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) is disposed. This is standard ADO.NET behavior.

For more information, see the base class [QueryField](/class/queryfield).
