<h5 class="center code-title">Bulk Insert</h5>

Brings all the data into the database in one-go via bulk insert. [Learn more](/operation/bulkinsert)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customers = GenerateCustomers();
    connection.BulkInsert<Customer>(customers);
}
```
