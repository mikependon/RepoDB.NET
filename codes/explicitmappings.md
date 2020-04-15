<h5 class="center code-title">Explicit Mapping</h5>

Create a much more descriptive class and properties by maximing the object mapping capability. [Learn more](/feature/classmapping)

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