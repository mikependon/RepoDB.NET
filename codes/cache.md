<h5 class="center code-title">Cache</h5>

**Avoid the frequent roundtrips** towards your database by placing the shared datasets into a **2nd-Layer cache**. [Learn more](#)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.QueryAll<Product>(cacheKey: "AllProducts");
}
```