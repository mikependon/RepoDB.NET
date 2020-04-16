<h5 class="center code-title">Bulk Delete</h5>

Identify the records to be removed in the application and delete it all a very speedy manner via bulk delete. [Learn more](/operation/bulkdelete)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var inactiveOrders = connection.Query<Order>(
        e => e.State == State.Inactive);
    connection.BulkDelete<Order>(inactiveOrders);
}
```