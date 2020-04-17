<h5 class="center code-title">Trace</h5>

Audit, debug and control the current execution context prior to the actual execution. [Learn more](/feature/tracing)

```csharp
public class CustomTrace : ITrace
{
    ...
}

using (var connection = CreateDbConnection(connectionString))
{
    connection.Query<Customer>(c => c.CustomerId == 10045,
        trace: new CustomTrace());
}
```
