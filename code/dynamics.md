<h5 class="center code-title">Dynamics</h5>

Work without even a data model in place with the use of dynamics. [Learn more](/feature/dynamics)

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
