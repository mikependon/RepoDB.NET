<h5 class="center code-title">Repository</h5>

Simplify the data access layers by leveraging the built-in repositories. [Learn more](/feature/repositories)

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(string connectionString) :
        base(connectionString) { }
}

using (var repository = new PersonRepository(connectionString))
{
    repository.Insert(person);
}
```