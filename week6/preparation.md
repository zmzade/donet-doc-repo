# Week 6 - Database

Databases are used for data persistence and .NET comes with a lot of packages that simplify consuming a lot of different database technologies. In this lesson we will focus on reading and writing data from a MySql database.

To connect to the database, we will use `[MySql.Data](http://MySql.Data)` package. Since this is just a raw wrapper for sending and receiving data, we will use `Dapper` - a simple object mapper to convert results of our queries to .NET objects.

## Installing MySql

Follow the instructions for your OS to install MySql on your machine.

If you want to use Docker, install Docker and run the following command:

```bash
docker run --name db -e MYSQL_ROOT_HOST=% -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 -d mysql/mysql-server

# To test connection
docker exec -it db mysql -uroot -p
# enter the password above (root)
# exit to exit
```

Now we need a connection string. For MySql, it is in the following format:

```bash
"Server=localhost;Database=;Uid=root;Pwd=root;"
```

Of course, replace the password with the password you used when you installed MySql.

## NuGet packages

Just like Node has npm packages, .NET has NuGet packages. They are installed via
command line invocation in the form of `dotnet add package PackageName`.

Unlike Node, there is only one type of packages and they are written to .csproj
file. Check the file after adding a package.

### Installing Dapper

To add Dapper, add the NuGet package with the following command:

```bash
dotnet add package Dapper
```

### Installing MySql

To use it with MySql, add NuGet package for MySql:

```bash
dotnet add package MySql.Data
```

## Reading data with MySql.Data

Let’s implement a simple endpoint that fetches the current users in MySql.

First we need to define a model for the data:

```csharp
public record User(string user);
```

Then, implement the endpoint with the following code:

```csharp
app.MapGet("/users", async () =>
{
    using (var connection = new MySqlConnection("Server=localhost;Database=;Uid=root;Pwd=root;"))
    {
        connection.Open();
        using (var command = new MySqlCommand("SELECT user FROM mysql.user", connection))
        {
            using (var reader = await command.ExecuteReaderAsync())
            {
                var users = new List<User>();
                while (await reader.ReadAsync())
                {
                    var user = reader.GetString(reader.GetOrdinal("user"));
                    users.Add(new User(user));
                }
                return users;
            }
        }
    }
});
```

So let’s analyze what we just did:

1. We created a new endpoint
2. Inside we created a new connection to MySql
3. We opened a connect
4. Then we created a new command with SQL text inside
5. We executed the command
6. In a loop we read rows and mapped columns to our model
7. Return the list to the caller

## Configuration

Connection strings should not be strings in the code and committed to source control. Instead, add them to the `appsettings.Development.json` file. Open it and add the following:

```json
{
  ... from before
  "ConnectionStrings": {
    "Default": "Server=localhost;Database=;Uid=root;Pwd=root;"
  }
}
```

Now, change the code to read it from the configuration:

```csharp
app.MapGet("/users", async (IConfiguration configuration) =>
{
    using (var connection = new MySqlConnection(configuration.GetConnectionString("Default")))
		...
});
```

This way we can have many connection strings securely stored outside of our code and available on demand.

Links:

- [Configuration in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-7.0)

### 🏗️ **Task:**

Change the code to select both `host` and `user` from the database:

1. Change the record for `User` and add `string host`
2. Change SQL to select `host`
3. Change the inner part of the loop to read user and host before creating a new object

## Dapper - a simple object mapper

SQL databases hold relational data while our code is working with objects - this requires us to convert rows of data to our objects which seems tedious. Let’s see how to simplify this!

Assuming the code from the first sample, we can rewrite it to:

```csharp
app.MapGet("/users2", async (IConfiguration configuration) =>
{
    // starting from C# 8 you no longer need inner {}
    using var connection = new MySqlConnection(configuration.GetConnectionString("Default"));

    var users = await connection.QueryAsync<User>("SELECT user FROM mysql.user");
    return users;
});
```

Doesn’t that seem much simpler?

## Working with our tables

Copy and paste and run the code from [`products.sql`](./products.sql) in your database either via MySql Workbench, command line or any other tool. Also, feel free to change the connection string to contain `Database=dapper;` to not repeat the database name all over the code.

Let’s implement the logic on top of it.

### Reading

Using the model:

```csharp
record Product(int Id, string Name, decimal Price);
```

Reading data is done with the following fragment:

```csharp
app.MapGet("/products", async (IConfiguration configuration) =>
{
    using var connection = new MySqlConnection(configuration.GetConnectionString("Default"));
    var products = await connection.QueryAsync<Product>("SELECT id, name, price FROM dapper.products");
    return products;
});
```

### Reading a single product

Implement the endpoint `/product/{id}`that:

- Changes the SQL to add `WHERE id = {id}`
- Instead of a list, returns `.FirstOrDefault`
- Bonus: if there is no such user, the list would be empty. What should REST API return when you want a resource that doesn’t exist? E.g. you call it with `/product/666`?
- Bonus: if the user passes negative number e.g. -1, what should REST API return in that case?

### Creating a product

Creating is done by handling POST endpoint.

```csharp
app.MapPost("/product", async (IConfiguration configuration, Product product) =>
{
    await using var connection = new MySqlConnection(configuration.GetConnectionString("Default"));
    var productId = await connection.ExecuteAsync("INSERT INTO dapper.products (name, price) VALUES (@name, @price)", product);

    // Status code 201
    return Results.Created($"/product/{productId}", product);
});
```

Try creating a new product by using a tool like Postman. Send the following JSON:

```json
{
  "name": "Test",
  "price": "12.99"
}
```

🏗️ **Task:**

- Implement validation to return 400 if the name is null or empty string.
- Bonus: Think what happens if you don’t send price in JSON?

### Updating a product - exercise!

Updating a product is done by handling a PUT endpoint on a `/product/{id}` endpoint.

The body is taken to update the product by running the following SQL:

```csharp
// assume that product is coming from body
await connection.ExecuteAsync("UPDATE products SET name=@name, price=@price WHERE id=@id", product);
```

What do you return from a PUT endpoint?

### Deleting a product

The last method of the CRUD endpoints - deletion!

To delete, run the following code:

```csharp
await connection.ExecuteAsync("DELETE FROM products WHERE id=@id", new { ID = 3 });
```

🏗️ **Task:**

- Implement an endpoint `/product/{id}` that takes that id and tries to delete the product with that ID
- Think about what should the endpoint return.
- What happens if there is no such product? What is the return of that endpoint?

## 🏗️ Final **Task:**

Modify the schema for the `product` table and add a new field:

```sql
`description` varchar(1000) NOT NULL DEFAULT '',
```

Now, modify all locations in the code to ensure this new field is returned, created and updated.

Bonus exercise 1: assume that the `description` is optional, how would you modify the code and where to ensure that creating a new Product with the following JSON doesn’t produce an error:

```json
{
  "name": "New product",
  "price": 12.99
}
```

Don’t forget to play with the code - you will learn a lot by trying to extend it in may ways. Create another table, create foreign keys, do JOINs. Model and implement a real problem!
