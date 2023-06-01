# Homework

## How to deliver homework

Open this template repository https://github.com/HackYourFuture-CPH/dotnet-masterclass and click on ![image](https://user-images.githubusercontent.com/6642037/115988976-3796da80-a5bc-11eb-9184-554a2218b2ae.png) and then create a copy of this structure on your own GitHub profile with the name `hyf-dotnet-masterclass`

Create a PR to add your homework to the respective week folder like you are used to do in the web development course, and if you don't remember how to do hand in homework using Pull Requests, please check here https://github.com/HackYourFuture-CPH/JavaScript/blob/master/javascript1/week1/homework.md

## Homework exercises for Week #4

Implement the following functionality (using `https://dummyjson.com/`)

- A `POST` endpoint that calls `POST users/add` with a record with `FirstName, LastName and Age` (this simulates creating a user)
- A `POST` endpoint that that calls `Post products/add` with a record with `Title and Price` (this simulates creating a product)
- A `POST` endpoint that takes a lists of ids and retrieves all of the users with those ids from the `GET users` (`Id, FirstName, LastName and Age`)
- A `POST` endpoint that takes a lists of ids and retrieves all of the products with those ids `GET products`(`Id, Title`)

> - You have until now gone throught classes, generic types and interfaces. Try to make use of this knowledge and see if it can help you to make the code easier to expand. (e.g. what if we want to easily add a new type like `comments`)

> - You can use the functionality from [DummyJson](https://dummyjson.com/) to simulate the data. It provides both `GET` and `POST` endpoints (e.g. [Get a product](https://dummyjson.com/docs/products#single), e.g. [Create a product](https://dummyjson.com/docs/products#add)) <br>
> - To test `POST` or `PUT` endpoints you can use [Insomnia](https://insomnia.rest/) or [Postman](https://www.postman.com/). <br>

---

> **_OPTIONAL:_**

- A `GET` endpoint that gets a user based on an id
- A `GET` endpoint that gets a product based on an id
- A `PUT` endpoint that updates a user based on an id and the body of the request
- A `PUT` endpoint that updates a product based on an id and the body of the request
- A `DELETE` endpoint that deletes a user based on an id
- A `DELETE` endpoint that deletes a product based on an id

<br>

# *_Examples_*

- Post endpoint (Recieve data in your api from Insomnia or postman for example)

```csharp

app.MapPost("/", (Post post) =>
{
    System.Console.WriteLine($"Title is [{post.Title}] and content is [{post.Content}]");
});

record Post(string Title, string Content);

/* 
Calling this endpoint with the input below:
{
    "title": "How was my day",
    "content": "My day was okay"
}

Will print this to the console:

"Title is [How was my day] and content is [My day was okay]"

/*

```


- Post endpoint (Send data from your api to another api)

```csharp

app.MapPost("/", async () =>
{
    var httpClient = new HttpClient();
    return await httpClient.PostAsJsonAsync("https://reqres.in/api/users",
        new Post("How was my day", "My day was okay"));
});

record Post(string Title, string Content);

```

- Mix both receiving data and passing it to anoter api
```csharp

app.MapPost("/", async (Post post) =>
{
    var httpClient = new HttpClient();
    var response = await httpClient.PostAsJsonAsync("https://reqres.in/api/users", post);

    if (!response.IsSuccessStatusCode)
    {
        return Results.BadRequest("Something went wrong");
    }

    var createdPost = response.Content.ReadFromJsonAsync<CreatedPost>();
    return Results.Ok(createdPost);
});


record Post(string Title, string Content);

record CreatedPost(int Id, string Title, string Content);

```
