<h5 class="center code-title">Class Mapping</h5>

Organize the names of your entity models by placing the correct mappings towards your database objects. [Learn more](/feature/classmapping)

```csharp
[Map("[sales].[Customer]")]
public class Customer
{
    [Primary, Identity, Map("CustomerId")]
    public int Id { get; set; }

    [Map("FName")]
    public string FirstName { get; set; }

    [Map("LName")]
    public string LastName { get; set; }
}
```