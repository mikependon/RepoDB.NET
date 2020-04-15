<h5 class="center code-title">Dynamics</h5>

Make a targetted operations by directly working with dynamics. [Learn more](/feature/dynamics)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entity = new
    {
        Id = 10045,
        Name = "James",
        LastUpdatedUtc = DateTime.UtcNow
    }
    var rows = connection.Update("[sales].[Customer]", entity);
}
```