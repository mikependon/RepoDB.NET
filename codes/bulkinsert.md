<h5 class="center code-title">Bulk Insert</h5>

Bring all the application data into the database in a very speedy manner via bulk insert. [Learn more](/operation/bulkinsert)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customers = GetNewCustomers();
    connection.BulkInsert<Customer>(customers);
}
```