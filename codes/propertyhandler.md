<h5 class="center code-title">Property Handler</h5>

Customize the column transformations in/out from your database with property handlers. [Learn more](/feature/propertyhandlers)

```csharp
public class Customer
{
    public int Id { get; set; }

    [PropertyHandler(typeof(CustomerAddressPropertyHandler))]
    public string Address { get; set; }
}
```