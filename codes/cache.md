<h5 class="center code-title">Cache</h5>

Avoid the frequent roundtrips in your database by placing the shared datasets into a cache object. [Learn more](/feature/caching)

```csharp
using (var connection = CreateDbConnection(connectionString))
{
    connection.QueryAll<Product>(cacheKey: "AllProducts");
}
```
