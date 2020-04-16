<h5 class="center code-title">Bulk Update</h5>

Process all changes in the client side and update it all in a very speedy manner via bulk update. [Learn more](/operation/bulkupdate)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orders = connection.Query<Order>(e =>
        e.DateUpdated < DateTime.UtcNow.Date.AddDays(-1));
    connection.BulkMerge<Order>(orders);
}
```