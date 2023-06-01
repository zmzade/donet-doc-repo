# Week 5 - Preparation

## Dependency injection

Dependency injection is a rather complex topic - but nevertheless, it’s very necessary as it’s used in almost all modern .NET applications.

Dependency Injection is a good idea because it helps to decouple the components of a software system, making them more modular, flexible, and easier to test. It allows for the creation of reusable and interchangeable components, making it easier to maintain and update the software over time. By separating concerns and providing dependencies explicitly, it also makes code easier to reason about and reduces the risk of bugs and errors.

### Dependency Injection is a “Design Pattern”

Dependency Injection is a way of designing software that follows a specific approach, which is called a "design pattern." The design pattern is like a blueprint that helps software developers solve common problems in a consistent and effective way. In the case of Dependency Injection, the pattern helps to separate the different components of the software and make them more modular and flexible, which makes it easier to maintain and update the software over time.

### The problem we need to solve

To really understand this, let’s look at a example:

*******************************We want to create a very simple blog on our website. The blogposts consist of a header and a body text. Like this:*******************************

```csharp
public class Blogpost
{
    public string Header { get; set; }
    public string Body { get; set; }
}
```

We will need some logic surrounding our blogpost - for example for listing all posts and saving a new post. For now we’ll keep it to a minimum - but we will make a little blog engine class:

```csharp
public class MyBlogEngine
{
    private List<Blogpost> _blogposts = new List<Blogpost>();

    public IEnumerable<Blogpost> ListAllBlogposts()
    {
        return _blogposts;
    }

    public void AddBlogpost(Blogpost post)
    {
        _blogposts.Add(post);
    }
}
```

Btw, note that this only stores the posts in memory (in the List). We’ll learn how to persist data later.

Now, when we want to use it, we could of course do this:

```csharp
//Using the blog engine directly
var myblog=new MyBlogEngine();
myblog.AddBlogpost(new Blogpost(){Header = "This is a post", Body = "This is the body of the post!"});
app.MapGet("/blog", () => myblog.ListAllBlogposts());
```

Which would work, but it would be a very **************tight coupling**************. The problem is that we would have to change this specific code, if in the future we get a better blog engine. Or - if we provide our code as a package to others, and they want to use a slightly different blog engine, they would have no way of changing which blog engine to use. Finally, sometimes you also want to do unit-testing of your code, where you exchange all dependencies (like the blog engine) with a mocked version to just test on each unit at a time - and that would also be hard when it’s a tight coupling.

### The solution: Dependency Injection

So, what we want to achieve is basically a **************loose coupling************** between the components in our code. And for that we’ll use Dependency Injection to keep track of which class to use for what. Essentially Dependency Injection is just a service that has a list of which other services should be used for what - and can help instantiate them.

First, we’ll define an interface (remember those?) for how a blog engine could look. An interface is important here, cause the goal of it all was to the flexibility of being able to change which classes (that adheres to the interface) we’ll use dynamically.

```csharp
public interface IBlogEngine
{
    void AddBlogpost(Blogpost post);
    IEnumerable<Blogpost> ListAllBlogposts();
}
```

Obviously, we also make sure that our engine implements that interface:

```csharp
public class MyBlogEngine : IBlogEngine
{
...
}
```

Now, in the beginning of our program we’ll want to configure this as a service in the dependency Injection Builder. Often in web applications, this is done in a separate StartUp class - but in our minimal API this happens at the top.

```csharp
var builder = WebApplication.CreateBuilder(args); //This line probably already exist
builder.Services.AddSingleton<IBlogEngine, MyBlogEngine>(); 
//Whenever anyone asks for an IBlogEngine give them an instance of "MyBlogEngine".
```

Notice how we register the blog engine as a *********Singleton.********* A [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern) (also a design pattern) means that it should create an object once (*******************new MyBlogEngine()*******************) and return the same object to everyone that asks for it.

Typical other options could be: 

- **************.AddTransient<IBlogEngine, MyBlogEngine>()**************  which means that a new instance of the blog engine will be created every time it is requested, or
- **************************************.AddScoped<IBlogEngine, MyBlogEngine>()************************************** which means that it will re-use the same object in the same scope (in our case each web request will be it’s own scope).

Now, that this is setup we can start using the Dependency Injection.

We can do this in multiple different ways. In our minimal API the simplest approach is this:

```csharp
//Using the blog engine from Dependency Injection
var blogengine = app.Services.GetService<IBlogEngine>();
blogengine.AddBlogpost(new Blogpost(){Header = "Hello!", Body = "This is an awesome blog post about something cool"});
app.MapGet("/blog", () => blogengine.ListAllBlogposts());
```

However this is by some considered an anti-pattern. A more preferred solution is the so-called *********************Constructor Injection*********************. This basically means that you simply list the components you need in your constructor (or in this case in your Map anonymous function) and then they will magically be provided for you. For example like this:

```csharp
app.MapGet("/blog", (IBlogEngine engine) => engine.ListAllBlogposts());
```

You can of course also include models posted by the user. Like this:

```csharp
app.MapPost("/blog/add", (Blogpost blogpost, IBlogEngine engine) => engine.AddBlogpost(blogpost));
```

🏗️**Task:**

- Implement a blog engine with an interface like the one above (you can copy the code).
- Register it in Dependency Injection by adding it to the Services collection (as shown above)
- Map paths that use the engine to list blog posts and add a new blog post
- Run & test it. You can test the post using Postman.
- Remember HttpClient from last week? Try to implement it again, but this time register it using *********************************builder.Services.AddHttpClient();*********************************  And then use Constructor Injection to get an IHttpClientFactory which you can use to create your client. Can you think of when this would be a good approach?

### Setting up Swagger with Dependency Injection (optional)

Most packages use Dependency Injection. Let’s try to look at an example. We’d like to install Swagger to explore our API.

First install the NuGet package for swagger, by running the following in a terminal:

```csharp
dotnet add package Swashbuckle.AspNetCore
```

And now register the Dependency Injection services (same place as you registered the Blog engine). Note, typically in add-ons these are wrapped in methods so you won’t have to register a lot manually.

```csharp
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
```

And add the swagger endpoints to the app, where you register the other endpoints:

```csharp
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}
```

Now you can run the code and go to `/swagger` on the website to see the Swagger UI for your API.

## Data Persistence

### General

An application creates data, then when the application quits. What happens to the data? This is where data persistence comes into play. In computer science, persistence refers to the characteristic of the state of a system that outlives the process that created it. When data is persisted it means that the exact same data can be retrieved by an application when it is running again.

There are various ways of persisting data, here are a few approaches:

- Saving data in a file.
- Saving your data to a local device database like MySQL, SQLite etc.
- Saving your data in a remote database like MySQL.
- Sending data to a server.
- Saving data to external storage, like a USB stick.

Format of the data that data can be saved to can differ and it usually depends on factors like:

- Technology - storage technologies have their own format or require you to send data in specific format (eg MongoDB uses JSON/BSON)
- Intention - some external system might require from you to store/export data in specific format eg [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) format
- Type of data: The type of data being stored can determine the most appropriate format. Numerical data may be best stored in a format that preserves precision, such as a binary format, while text data may be best stored in a plain text format.
- Size of data: The size of the data can also influence the choice of format, as some formats are more efficient for storing large amounts of data than others. For example, binary formats are often used for storing large datasets, as they can compress data and save storage space.
- Security requirements: If the data contains sensitive or confidential information, a format that provides encryption or other security features may be necessary.

## Storing data on a file system

Using `.NET` we can write and read from the file system. `File` is a static class in .NET that provides static methods for the creation, copying, deletion, moving, and opening of a single file.

Most commonly used operations with `File` are Copy, Create, Delete, Open, and Read. Almost all operations come with the option to work with (text) lines, bytes, text. Some of the methods like Read and Write support asynchronous operations (eg `File.WriteAllTextAsync`)

A simple example of writing a file:

```csharp
File.WriteAllText("test.txt", "some text"); // without path
File.WriteAllText(@"C:\dev\test.txt", "some text"); // absolute path
await File.WriteAllTextAsync(@"C:\dev\aa\test.txt", "some text"); // async call
File.WriteAllText(@"X:\test.txt", "some text"); // ❌ drive X does not exist, this will throw an IO error
```

> Note: if path is not exist or you don’t have permissions to write to the folder or file .NET will throw an IO Exception.
> 

## Comparison with JavaScript

```jsx
const fs = require("fs");

// writing to a file
fs.writeFileSync(
    "test.json",
    "some text",
    "utf8",
    (error) => console.log(error)
);

// reading from the file
const content = fs.readFileSync("test.json", "utf8", (error) => console.log(error));
console.log(content );
```

🏗️**Task:**

- Try writing text to a file.
- Find a method in `File` class that you can use to append text to the file from the previous step. Try using VSCode intelligence before searching in google.

## Storing JSON

We have seen how simple text can be saved in the file and as simple it can be with the text it is really not a big difference to do the same with the JSON!

Here is an example of how an object (BlogPost) ca be serialized to a JSON and saved in a file.

```csharp
Blogpost blogPost = new Blogpost() { Header = "blog post", Body = "Lorem ipsum blog post" }

// serializing object to a JSON and writing to a file synchronously
string json = System.Text.Json.JsonSerializer.Serialize(blogPost);
File.WriteAllText("blogPost.json", json);

// serializing object to a JSON and writing to a file asynchronously
string json = System.Text.Json.JsonSerializer.Serialize(blogPost);
await File.WriteAllTextAsync("blogPost.json", json);
```

Now, even if you shut down your application you will be able to read data after you run it again. Here is an example of how to load data from the file.

```csharp
// reading file synchronously and deserializing to an object
var json = File.ReadAllText("blogPost.json");
return System.Text.Json.JsonSerializer.Deserialize<Blogpost>(json);

// reading file asynchronously and deserializing to an object
var json = await File.ReadAllTextAsync("blogPost.json");
return System.Text.Json.JsonSerializer.Deserialize<Blogpost>(json);
```

> Note: we can use `SerializeAsync` and `DeserializeAsync` but that requires knowledge of the Stream(s) that we are not covering in the class.
> 

🏗️**Task:**

- Modify `MyBlogEngine` to load and store data from/to the file instead of in-memory list. Be careful, the example above is handling a single blog post. When you are saving a new blog post first you need to load the existing blog post list from the file system and then you need to append a new blog post. After that you can serialize list to a JSON and save it to a file system.

## Links

- [https://en.wikipedia.org/wiki/Persistence_(computer_science)](https://en.wikipedia.org/wiki/Persistence_(computer_science))
- [https://www.mongodb.com/databases/data-persistence](https://www.mongodb.com/databases/data-persistence)
- [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/serialization/walkthrough-persisting-an-object-in-visual-studio](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/serialization/walkthrough-persisting-an-object-in-visual-studio)

