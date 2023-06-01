# Homework for week 6

Let's take a look at a simple endpoint:

```csharp
app.MapGet("/users2", async (IConfiguration configuration) =>
{
    // starting from C# 8 you no longer need inner {}
    using var connection = new MySqlConnection(configuration.GetConnectionString("Default"));

    var users = await connection.QueryAsync<User>("SELECT user FROM mysql.user");
    return users;
});
```

We want to move this code to a class paired with an interface. Create a new file
and name it `Users.cs`. Start a file with a namespace:

```csharp
namespace HackYourFuture.Week6;
```

Everything will be written after the namespace declaration. Then, move the
`User` class or record (whichever you have) to the file. From now on, we keep
adding to the file.

Let's introduce the interface:

```csharp
public interface IUserRepository
{
    Task<IEnumerable<User>> GetUsers();
}
```

Then, the class that implements it is typically written as:

```csharp
public class UserRepository : IUserRepository
{
    private string connectionString;

    public UserRepository(IConfiguration configuration)
    {
        this.connectionString = configuration.GetConnectionString("Default");
    }

    public Task<IEnumerable<User>> GetUsers()
    {
        using var connection = new MySqlConnection(connectionString);

        var users = await connection.QueryAsync<User>("SELECT user FROM mysql.user");
        return users;
    }
}
```

Then, we need to register it in the `Program.cs`:

```csharp
builder.Services.AddScoped<IUserRepository, UserRepository>();
```

Don't forget to add `using HackYourFuture.Week6;` to the top of the file.

Finally! We can rewrite the original endpoint to:

```csharp
app.MapGet("/users", async (IUserRepository userRepository) =>
{
    return await userRepository.GetUsers();
});
```

## Task

Following the recipe above, move the rest of the code in the appropriate file.

Keep in mind that all methods concerning users go to `Users.cs` file while
methods for dealing with `Product` go to `Products.cs` file.

In larger projects we keep interface in a separate file from the class and the
file name is named as the interface or class in it.
