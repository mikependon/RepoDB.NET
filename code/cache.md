<h5 class="center code-title">Cache</h5>

Avoid the frequent roundtrips in your database by placing the shared datasets into a cache object. [Learn more](/feature/caching)

```csharp
var cache = CacheFactory.GetCacheInstance();
using (var connection = new SqlConnection(connectionString))
{
    connection.QueryAll<Product>(cacheKey: "AllProducts", cache: cache);
}
```
