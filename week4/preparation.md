# Week 4 - preparation: Async and await

## What is async/await?

Async/await is a feature in C# that allows you to write asynchronous code. Asynchronous code is code that can perform multiple tasks concurrently (in the same time), which can help improve performance and responsiveness in certain situations, such as when making web requests or accessing a database.

The `async` keyword is used to define a method as asynchronous, and the `await` keyword is used to pause the execution of the method until a task is complete. When the method is paused, control is returned to the calling method, allowing other code to execute. When the awaited task is complete, the method resumes execution.

<br/>

## Comparison with JS

In plain javascript we used the following style for handling asynchronous code:

```js
function getResults(q) {
  return fetch(`https://dummyjson.com/users/search?q=${q}`)
    .then((r) => r.json())
    .then((data) => {
      return data;
    });
}
```

For those who played with TypeScript, it might look like:

```ts
async function getResults(q) {
  const response = await fetch(`https://dummyjson.com/users/search?q=${q}`);
  const data = await response.json();
  // use data
  return data;
}
```

In C# we prefer using `async`/`await`:

```csharp
public async Task GetResult(string q)
{
    var response = await new HttpClient().GetAsync($"https://dummyjson.com/users/search?q={q}");
    if (!response.IsSuccessStatusCode)
    {
        throw new Exception($"Uh-oh!: {response.StatusCode}");
    }

    // prefer having a proper type
    var data = response.Content.ReadFromJsonAsync<object>();
    // cannot use this data for much...
}
```

In this example, the `GetResult` method is defined as asynchronous using the `async` keyword. Inside the method, an instance of the HttpClient class is created and used to make a web request to the URL "`https://dummyjson.com/users/search?q={q}`". The `await` keyword is used to pause execution of the method until the web request completes. Once the request is complete, the response is read using the `ReadFromJsonAsync` method and the result is returned.

### `.ContinueWith`

`Promise<>` in JS/TS matches `Task<>` in C#. Instead of `.then` one could use `.ContinueWith`:

```csharp
var data = new HttpClient().GetAsync("https://dummyjson.com/users/search?q=Sheldon")
    .ContinueWith(t =>
    {
        // Unlike in .then, t here is a Task which contains Result
        // we need to "unwrap" it
        var response = t.Result;
    });
```

>__*Note:*__ As this style of writing async code in C# is rarely used, it is here merely to
demonstrate that C# and JS are quite similar under the hood. If you want to
learn more about this, check out [Task Parallel Library](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl), but
note that it is quite advance.

<br/>

## What is a task?

A `Task` is an object that represents some work that should be done. The task can tell you if the work is completed and if the operation returns a result, the task gives you the result. In short, a `Task` represents an asynchronous operation that may or may not return a value.

When a method returns a `Task`, it means that the method is doing some work in the background and will notify the caller when it is finished. The caller can then continue with other work while the background task is being executed.

Here's an example of how to create and use a `Task` in C#:

```csharp
async Task<object> GetResultAsync(string url)
{
    var client = new HttpClient();
    var response = await client.GetAsync(url);
    var result = await response.Content.ReadFromJsonAsync<object>();
    return result;
}

async Task UseResultAsync()
{
    var url = "https://dummyjson.com/users/search?q=Sheldon";
    Task<object> task = GetResultAsync(url);

    // Do some other work while the task is being executed.

    var result = await task;
    // Use the result when the task is complete.
}

```

In this example, the `GetResultAsync` method is defined to return a `Task<object>`, indicating that it is an asynchronous operation that will return a `object`. The method uses the `HttpClient` class to make a web request and returns the result as a `object`.

The `UseResultAsync` method creates a `Task<object>` by calling `GetResultAsync`. While the task is being executed, the method can do other work. Once the task is complete, the result is obtained using the `await` keyword and can be used for further processing.

<br/>

## Typing responses

Unlike JavaScript, in C# you need to specify types upfront when deserializing.
This presents a small challenge.

Let's take a look at the following example:

```csharp
var response = await new HttpClient().GetAsync("https://dummyjson.com/users/search?q=Sheldon");
var data = await response.Content.ReadFromJsonAsync<object>();
Console.WriteLine(System.Text.Json.JsonSerializer.Serialize(data));
```

It will print out:

```json
{
  "users": [
    {
      "id": 2,
      "firstName": "Sheldon",
      "lastName": "Quigley",
      "maidenName": "Cole",
      [...]
      "address": {
        "address": "6007 Applegate Lane",
        [...]
      },
      "macAddress": "13:F1:00:DA:A4:12",
      "university": "Stavropol State Technical University",
      "bank": {
        "cardExpire": "10/23",
        [...]
      },
      "company": {
        "address": {
          "address": "8821 West Myrtle Avenue",
         [...]
        },
        "department": "Services",
        "name": "Aufderhar-Cronin",
        "title": "Senior Cost Accountant"
      },
      "ein": "52-5262907",
      "ssn": "447-08-9217",
      "userAgent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/534.30 (KHTML, like Gecko) Ubuntu/11.04 Chromium/12.0.742.112 Chrome/12.0.742.112 Safari/534.30"
    }
  ],
  "total": 1,
  "skip": 0,
  "limit": 1
}
```

How do we get to those properties? The answer is by writing our own types.
First, take a look at the root: it starts with `{` which means this is an object.

Let's define our response type:

```csharp
class DummyResponse {}
```

Now let's use it:

```csharp
var response = await new HttpClient().GetAsync("https://dummyjson.com/users/search?q=Sheldon");
var data = await response.Content.ReadFromJsonAsync<DummyResponse>();
Console.WriteLine(System.Text.Json.JsonSerializer.Serialize(data));
```

The code will print out `{}`. Where did all our data go?
When deserializing, we chose to put _some_ data on the types and only that data will be used.

Let's change the type to:

```csharp
class User
{
    public int Id { get; set; }
    public string FirstName { get; set; }
}
class DummyResponse
{
    public List<User> Users { get; set; }
}
```

Running the fetching code we get:

```json
{ "Users": [{ "Id": 2, "FirstName": "Sheldon" }] }
```

Exercise:

- Keep adding properties from the response to C# code.
- Try playing with the types.
- Can you get typed version to contain _all_ the original data?
- Try using `FirstName`, `firstName` and `First_Name` in the above example

<br/>

## For those who want to know more

C# 9 introduces [records](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/records) which simplify a lot of code, but it is still new and there are rough edges which require playing a lot with them. The above code can be simplified as

```csharp
record User(int Id, string FirstName);
record DummyResponse(List<User> Users);
```

As this is a C# 9 feature, it might be available in lots of existing codebases.

<br/>

## Working with async/await

There are situations where you need to retrieve multiple pieces of data concurrently. The `Task` class contains two methods, `Task.WhenAll` and `Task.WhenAny`, that allow you to write asynchronous code that performs a non-blocking wait on multiple background jobs.

This example with `Task.WhenAll` shows how you might grab User data for a set of userIds and return all when they are ready.

```csharp
async Task<User> GetUserAsync(int userId)
{
    // Code omitted:
    //
    // Given a user Id {userId}, retrieve a user with that id
}

async Task<IEnumerable<User>> GetUsersAsync(IEnumerable<int> userIds)
{
    var getUserTasks = new List<Task<User>>();

    // Loop over the list of ids to create a task for each
    foreach (int userId in userIds)
    {
        // Create a new task
        var getUserTask = GetUserAsync(userId);

        // Add the task to the list of tasks
        getUserTasks.Add(getUserTask);
    }

    // Wait until all of the tasks are completed before returing
    return await Task.WhenAll(getUserTasks);
}
```

This example with `Task.WhenAny` shows how you might grab User data for a set of userIds and log when any of the tasks are done.

```csharp
async Task<User> GetUserAsync(int userId)
{
    // Code omitted:
    //
    // Given a user Id {userId}, retrieves a User object corresponding
    // to the entry in the database with {userId} as its Id.
}

async Task GetUsersAsync(IEnumerable<int> userIds)
{
    var getUserTasks = new List<Task<User>>();

    // Loop over the list of ids to create a task for each
    foreach (int userId in userIds)
    {
        // Same as above, but in one line. Create a new task and add it directly in the list
        getUserTasks.Add(GetUserAsync(userId));
    }

    // While the list from above has tasks, loop over them
    while (getUserTasks.Any())
    {
        // When any of the tasks is done assign it to the variable
        var user = await Task.WhenAny(getUserTasks);

        // We then have to remove the task from the list of tasks as it is completed
        getUserTasks.Remove(user);

        // Log to the console the retrieval of each user
        System.Console.WriteLine($"User {user.Name} was retrieved");
    }
}
```

## Usefull following material:

- [Task asynchronous programming model](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/task-asynchronous-programming-model)
- [Asynchronous programming](https://learn.microsoft.com/en-gb/dotnet/csharp/asynchronous-programming/async-scenarios)
- [Video: Introduction To Async, Await, And Tasks](https://www.youtube.com/watch?v=X9N5r6kMOxw)
- [Async explained with common scenario](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming)


## Exercises
1. __Get item by id__: Create a new `GET` endpoint that does the following
    - takes an `id` as a query parameter (`int`)
    - executes an async `GET HTTP` call to an endpoint using the id from the query parameter
    - checks if the response is successful
    - returns the response or `Results.BadRequest` if the response fails

> **_NOTE:_**
>- a public api that returns dummy data can be found here: 
    https://dummyjson.com/; e.g. https://dummyjson.com/products/1
>- to check if an api call is successful the `HttpResponseMessage` has a property called `IsSuccessStatusCode` e.g. `response.IsSuccessStatusCode`
>- to return an object from a response you can use 
    `ReadFromJsonAsync<object>()` instead of `ReadAsStringAsync()`

2. __Get users with name__: Create a new `GET` endpoint that does the following:
    - takes a `name` as a query parameter 
    - validates that the `name` is not null or an empty string (if it is it returns a `BadRequest`)
    - calls (async) the same dummy endpoint and returns a list of users (id, firstName, lastName) that have been found
> **_NOTE:_**
>- the dummy page provides this endpoint that you can use: https://dummyjson.com/users/search?q=`searchquery`
>- the response from the endpoint will be an object which will contain a list of users with a lot of data
    >> - you only need to return the 3 properties specified above
    >> - use a mix of `ReadFromJsonAsync` and define 2 new classes
    >> - One would be the main `Response` (the response with a List of Users inside) 
    >> - and the second would be the actual `User` class in which you can define the required properties.

---

## Extra:
3. __Get products with ids__: Create a new `GET` endpoint that does the following:
    - takes `names` as a comma separated string as a query parameter (e.g. `john,ann,alex`)
    - splits the string by comma and and removes any white spaces
    - for each name calls an async `GetUserByName` method which contains an http call to the dummy url. (The method should return the `Task<User>`)
    - `WhenAll` the `Task`s are completed, the list of users is returned.
> **_NOTE:_**
> - the http call in `GetUserByName` will return you a list of users, you can just return the `FirstOrDefault` user from this list for simplicity. (so you can have `Task<User>`)