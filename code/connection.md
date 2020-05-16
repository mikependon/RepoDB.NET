<h5 class="center code-title">Connection</h5>

Simply instantiate a connection object (with proper connection string) and you can then use it right away. [Let's get started](/tutorial/get-started-sqlserver)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Customer, int>(new Customer
    {
        Name = "John",
        Doe = "Doe")
    });
}
```
