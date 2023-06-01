# Week 1 - lesson plan

## Agenda

- Q&A about preparation materials
- Let's take a look at preparation materials (Ivan)
- Let's talk about .NET (Toni)
  - Creating and running .NET programs
  - Types - C# is statically typed language
  - Working with arrays

## Exercises

### 1. Write a GET endpoint that receives two numbers and returns a sum

The call to `http://localhost:5000/api/example?a=1&b=2` should return 3.

> The names of the parameters or the route doesn't matter, feel free to rename!

### 2. Write a GET endpoint that receives a string and returns a JSON object with that string

The call to `http://localhost:5000/api/example?str=helloworld` should return

```json
{
  "content": "helloworld"
}
```

Let's talk spaces!

> The names of the parameters or the route doesn't matter, feel free to rename!

### 3. GET endpoint that receives a number parameter: try sending any text

The call to `http://localhost:5000/api/example?number=1` should work, what about
calling `http://localhost:5000/api/example?number=a`?

Let's talk error handling.

### 4. `int.TryParse` and if-else combination (report error)

Sometimes the input needs to be validated to be correct. Let's say the input is
a string value - how to check if it is a valid number?

```csharp
string input = "This is a value";
if (int.TryParse(input, out int value))
{
  // input _was_ a string and value is now initialized
  value ++;
}
else
{
  // The string in input is not a valid number
}
```

Rewrite the endpoint from the first exercise to accept string. Then, check if
the passed input is number and return an error if it is not.

If you have any questions, we would love to get them before, during and after
the class!
