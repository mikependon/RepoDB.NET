<h5 class="center code-title">Bulk Delete</h5>

Remove the big chunks of data in one-go via bulk delete. [Learn more](/operation/bulkdelete)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var inactiveOrders = connection.Query<Order>(
        e => e.State == State.Inactive);
    connection.BulkDelete<Order>(inactiveOrders);
}
```