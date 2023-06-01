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
// Don't forget to put this on the top!
using System.Net.Http.Json;

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

As this style of writing async code in C# is rarely used, it is here merely to
demonstrate that C# and JS are quite similar under the hood. If you want to
learn more about this, check out [Task Parallel Library](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl), but
note that it is quite advance.

### Typing responses

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
      "age": 28,
      "gender": "male",
      "email": "hbingley1@plala.or.jp",
      "phone": "\u002B7 813 117 7139",
      "username": "hbingley1",
      "password": "CQutx25i8r",
      "birthDate": "2003-08-02",
      "image": "https://robohash.org/doloremquesintcorrupti.png",
      "bloodGroup": "O\u002B",
      "height": 187,
      "weight": 74,
      "eyeColor": "Brown",
      "hair": { "color": "Blond", "type": "Curly" },
      "domain": "51.la",
      "ip": "253.240.20.181",
      "address": {
        "address": "6007 Applegate Lane",
        "city": "Louisville",
        "coordinates": { "lat": 38.1343013, "lng": -85.6498512 },
        "postalCode": "40219",
        "state": "KY"
      },
      "macAddress": "13:F1:00:DA:A4:12",
      "university": "Stavropol State Technical University",
      "bank": {
        "cardExpire": "10/23",
        "cardNumber": "5355920631952404",
        "cardType": "mastercard",
        "currency": "Ruble",
        "iban": "MD63 L6YC 8YH4 QVQB XHIK MTML"
      },
      "company": {
        "address": {
          "address": "8821 West Myrtle Avenue",
          "city": "Glendale",
          "coordinates": { "lat": 33.5404296, "lng": -112.2488391 },
          "postalCode": "85305",
          "state": "AZ"
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

### For those who want to know more

C# 9 introduces [records](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/tutorials/records) which simplify a lot of code, but it is still new and there are rough edges which require playing a lot with them. The above code can be simplified as

```csharp
record User(int Id, string FirstName);
record DummyResponse(List<User> Users);
```

As this is a C# 9 feature, it might be available in lots of existing codebases.
