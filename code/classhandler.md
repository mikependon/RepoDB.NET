<h5 class="center code-title">Class Handler</h5>

Handle the serialization/deserialization process of the model from/to your database with class handlers. [Learn more](/feature/classhandlers)

```csharp
[ClassHandler(typeof(CustomerClassHandler))]
public class Customer
{
    public int Id { get; set; }

    public Address Address { get; set; }
}
```
