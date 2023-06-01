## ****Minimal APIs****

> [https://learn.microsoft.com/en-gb/aspnet/core/fundamentals/minimal-apis?view=aspnetcore-7.0](https://learn.microsoft.com/en-gb/aspnet/core/fundamentals/minimal-apis?view=aspnetcore-7.0)
> 

We have talked and been doing some endpoints in the .NET, but it is a good time to tell more about Minimal APIs.

Minimal APIs are a clean and straightforward way to build small microservices and HTTP APIs in [.](http://asp.net/)NET. It is an alternative way of building HTTP APIs (there are other approaches, but we will not cover).

No complicated scaffolding, no unnecessary dependencies. Minimal APIs hook into [ASP.NET](http://asp.net/) Core’s hosting and routing capabilities and allow you to create fully functioning APIs with just a few lines of code.

```csharp
var app = WebApplication.Create(args);

app.MapGet("/", () => "This is a GET");
app.MapPost("/", () => "This is a POST");
app.MapPut("/", () => "This is a PUT");
app.MapDelete("/", () => "This is a DELETE");

app.Run();
```

**Parameter binding**

Parameter binding is the process of converting request data into strongly typed parameters that are expressed by route handlers. A binding source determines where parameters are bound from. Binding sources can be explicit or inferred based on HTTP method and parameter type.

Supported binding sources:

- Route values
- Query string
- Header
- Body (as JSON)
- Services provided by dependency injection
- Custom

**Route parameters** can be captured as part of the route pattern definition:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// binding route parameters to a typed value
// GET /users/3/books/7
app.MapGet("/users/{userId}/books/{bookId}", // route pattern
    (int userId, int bookId) // route parameters bind to variables
			=> $"The user id is {userId} and book id is {bookId}");

app.Run();
```

The preceding code when navigating to `/users/3/books/7` returns `The user id is 3 and book id is 7` from the URI `/users/3/books/7`.

**Query parameters** can be captured as part of the query string

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Bind query string values to a primitive type array.
// GET  /square?number=4
app.MapGet("/square", (int number) =>
                      $"square of {number} is {number*number}");

app.Run();
```

**Binding arrays**

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// Bind query string values to a primitive type array.
// GET  /tags?q=1&q=2&q=3
app.MapGet("/tags", (int[] q) =>
                      $"tag1: {q[0]} , tag2: {q[1]}, tag3: {q[2]}");

app.Run();
```

**Explicit parameter binding**

Attributes can be used to explicitly declare where parameters are bound from.

```csharp
using Microsoft.AspNetCore.Mvc;

var builder = WebApplication.CreateBuilder(args);

var app = builder.Build();

// GET /2?number=3
app.MapGet("/{id}", ([FromRoute] int id,
        [FromQuery(Name = "number")] int number,
        [FromServices] ILogger<Program> logger)
    =>
{
    logger.LogInformation("Logger service");
    return $"Route parameter id: {id}; Query Parameter number: {number}";
});

app.Run();
```

**Logging**

```csharp
var app = WebApplication.Create(args);

app.MapGet("/", () =>
{
	app.Logger.LogInformation("Printing hello world"); // this will be shown in console
	return "Hello World"
});

app.Run();
```

## Data Types

C# is a strongly-typed language - it requires the programmer to specify the data type of every value and expression. While it means writing more code, using types has long-term benefits like built-in documentation and increased readability.

C# has several built-in data types. A complete list of .NET built-in types can be found [here](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/built-in-types).

You don’t need to memorise all of them, but pay specific attention to these common ones that we’ll use throughout our masterclass:

- `int` - whole numbers, like: 1, -56, 948
- `decimal`- decimal numbers, like: 239.43909, -660.01
- `char` - single characters, like: “a”, “&”, “Æ”
- `string` - string of characters, like: “dog”, “hello world”
- `bool` - boolean values, like: true or false

## Variables

In C#, data types and variables are closely intertwined. Remember how C# is strongly typed? Every time we declare a variable, we have to specify what kind of data type that variable is going to hold.

There are two ways we can assign variables. We can do it on two lines:

```csharp
// Declare an integer
int myNumber;
myNumber = 42;
```

Or, we can be more concise and just do it on one:

```csharp
// Declare an integer
int myNumber = 42;
```

Some other examples of variable declaration

```csharp
double myDoubleNum = 5.99D;  // Floating point number
char myLetter = 'D';         // Character
bool myBool = true;          // Boolean
string myText = "Hello";     // String
int myNumber = 42;           // Integer (whole number)
```

Once a variable has been defined, you can not change its type.

```csharp
int myNumber = 42;
myNumber = "Text"; ❌
```

**What is `var` in C#?**

In C#, the `var` keyword can **implicitly** define a variable's type based on its assigned value. For example, `var myNumber = 42;` would define a variable called `myNumber` with a type of `int`, since `42` is an integer. Using `var` can save you from explicitly specifying the variable type, especially when the type is long or complex. However, it is essential to note that the variable type is still determined at compile time and cannot change during runtime.

**What is the difference between `var` in C# and Javascript**

In JavaScript, the `var` keyword is also used to declare variables, but it does not necessarily define the type of the variable. Instead, the type of the variable is determined by the value assigned to it. This means that a variable declared with `var`, in Javascript, can be reassigned to a different type of value later on. 

```csharp
var myNumber = 42;        // Integer (whole number)
var myDoubleNum = 5.99D;  // Floating point number
var myLetter = 'D';       // Character
var myBool = true;        // Boolean
var myText = "Hello";     // String
```

Even when you define a variable with `var` you can’t change its type because the type is inferred from the assignment, and the compiler will know if you try to cheat it.

```csharp
var myNumber = 42;
myNumber = "Text"; ❌
```

### Conclusion

Variables and types are an essential part of programming in C#. By learning how to declare, assign, and use variables, you can build more complex programs that manipulate data in interesting and useful ways.

⚒**Task:**

- Declare a variable of type `object` and assign it a value of your choice. Then, use type casting to convert the value to a string and print it to the console.

## Methods

In C#, methods can be defined using the following syntax:

```csharp
[access modifier] [return type] [method name]([parameters])
{
   // method body
   [return statement];
}

// If the method does not return a value then the return type is marked as void
public void PrintHelloWorld()
{
    //...code
}

public int[] Reverse(int[] array)
{
    //...code	
}
```

Here is a breakdown of each component of the syntax:

- Access modifier: Specifies the accessibility of the method. It can be public, private, protected, or internal.
- Return type: Specifies the data type of the value that the method returns. It can be any valid C# data type or **void** if the method does not return a value.
- Method name: Specifies the name of the method. It should be a meaningful name that describes the purpose of the method.
- Parameters: Specifies the input values to the method. It can be any valid C# data type or a user-defined type.
- Method body: Contains the code that defines the functionality of the method.
- Return statement: Specifies the value that the method returns. It is optional and only needed if the method has a return type other than void.

Here is an example of an Get method (from Minimal API) calling a method that takes two integers and returns their sum:

```csharp
app.MapGet("/", (int a, int b) => Add(a,b));

int Add(int a, int b)
{
    int sum = a + b;
    return sum;
}

app.Run();
```

⚒**Task:**

- Write a method **`Reverse`** that takes an array of integer values as a parameter and reverses the order of the elements in the array.

```csharp
public void Reverse(int[] array)
{
    // your code here
}
```

## Generic types in C#

> base docs: [https://learn.microsoft.com/en-us/dotnet/standard/generics/](https://learn.microsoft.com/en-us/dotnet/standard/generics/) and [https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/generic-type-parameters](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/generic-type-parameters))
> 

Generics let you define class, method, interface or structure without specifying its actual data type. In other words, it's a way to create reusable code that can work with different data types. A method declared with the type parameters for its return type or parameters is called a generic method.

General form of generic method:

```csharp
// With return type T
public T GetRandom<T>(T param)
{
	
}

// With return T
public T GetRandom<T>(T param)
{
	
}
```

To understand this better, consider a following example:

```csharp
private static string GetRandom(string[] array)
{
	Random random = new Random();
	int index = random.Next(0, array.Length);
	return array[index];
}
```

The function only works with the string. If we want to work with another type we would have to write another funciton with the same logic.

```csharp
private static int GetRandom(int[] array)
{
	Random random = new Random();
	int index = random.Next(0, array.Length);
	return array[index];
}
```

A better approach would be to create a generic method that would handle both cases.

Here's an example of a generic version of the GetRandom function:

```csharp
// Method that accepts array of generic type and returns single element of the type
private static T GetRandom<T>(T[] array)
{
	Random random = new Random();
	int index = random.Next(0, array.Length);
	return array[index];
}
```

And example calling GetRandom:

```csharp
int[] integers = { 1, 2, 3, 4, 5 };
int randomInt = GetRandom(integers);

string[] strings = { "foo", "bar", "baz" };
string randomString = GetRandom(strings);
```

Generics are most frequently used with collections and the methods that operate on them. An good example of generics is `List<T>` that we will cover next.

## Lists

> base docs: [https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/tutorials/arrays-and-collections](https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/tutorials/arrays-and-collections))
> 

In C# a `List` is a collection of objects that can be dynamically resized. It is similar to an array,  but has the ability to grow and shrink as needed, whereas an array has a fixed size.

A `List` can hold objects of any type (as long as they are all the same type), and can be accessed and modified using a variety of methods and properties. It provides a number of useful features, such as sorting, searching, and filtering.

Key features of Lists:

- Dynamic resizing: items can be added and removed as needed (unlike arrays that have a fixed size)
- Strong typing: you can only add objects of a specific type to the collection
- Index-based access: You can access items in a List using an index. The syntax for accessing an item is `list[index]`, where `index` is the zero-based index of the item you want to access.

A list can be declared and used in the following manner:

```csharp
// Create a new list of integers
var numbers = new List<int>(); // or List<int> numbers = new List<int>();

// Add some numbers to the list.
numbers.Add(5);
numbers.Add(10);
numbers.Add(15);

// Access the first element of the list.
var firstNumber = numbers[0];

// Remove the second element of the list.
numbers.RemoveAt(1);

// Print out the remaining elements of the list.
foreach (int number in numbers)
{
    Console.WriteLine(number);
}
```

If you know from the beginning some or all of the items that the list will contain you can declare it like this:

```csharp
var numbers = new List<int>(){1, 2, 3};
```

Comparison between C# functionality and React:

| C# | React |
| --- | --- |
| list.Add(item) | array.push(<item>) |
| list.AddRange(list2) | array1.concat(array2) |
| list.Exists(element => <condition>) | array.some(element => <condition>) |
| list.Find(element => <condition>) | array.find(element => <condition>) |
| list.FindAll(element => <condition>) | array.filter(element => <condition>) |
| list.ForEach(<Method>) | array.forEach(element => <condition>) |
| list.IndexOf(item) | array.indexOf(item) |
| list.Insert(index, item) | array.splice(startIndex, deleteCount, item) |
| list.Sort() | array.sort() |

Exercises:

- Declare a list of type **`int`** and assign some random values. Then return an object that contains two lists, one with even numbers and one with uneven numbers.
- Declare a new list of type `char` . Declare a new variable of type `string` and assign a random sentence. Populate the list of `char` with the characters from the sentence.
- Using the `char` list from above find how many items are not vowels.
- Using the `char` list from above return the index of the first occurrence of letter `s` and the index of the last occurrence of letter `g`
- Declare a list of `int` and add the numbers from 0 to 100 to it.
- In the `int` list from above remove the items from 33 (inclusive) to 44 (inclusive)
- Split the `int` list from above in 2 lists and return the two lists, one ordered from smallest to largest number, and one from largest to smallest.
