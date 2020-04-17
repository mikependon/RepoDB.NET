<h5 class="center code-title">Enumeration</h5>

Organize your class lookups via enumerations. [Learn more](/feature/enumeration)

```csharp
using (var connection = CreateDbConnection(connectionString))
{
    connection.QueryAll<Customer>(e => e.Gender == Gender.Male);
}
```
