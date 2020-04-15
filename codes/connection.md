<h5 class="center code-title">Connection</h5>

Simply **instantiate** a connection object (with proper connection string) and you can then use it **right away**. [Learn more](#)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Customer>();
}
```