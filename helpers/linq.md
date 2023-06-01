# LINQ to Objects

[LINQ](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/linq-to-objects) is a functional and short way to use a declarative syntax for
operations on top of collections. It typically replaces `foreach` loops when it
makes sense.

> Note: Keep in mind that LINQ operations always produce new collections, the old
> ones are not modified!

## Sum

```csharp
// assume IEnumerable<double> numbers
// e.g.
List<double> numbers = /* ... */

double sum = 0;
foreach (var number in numbers)
{
    sum += number
}
```

This can be replaced with:

```csharp
var sum = numbers.Sum();
```

## Filtering

Let's say you want to filter out users that have age above 40.

```csharp
class User
{
    public int Age { get; set; }
}
List<User> users = /* ... */

// also can use var here
List<User> younger = users.Where(u => u.Age < 40).ToList();
```

## Mapping

Let's say you want to extract a property for each element inside:

```csharp
public record User(string FirstName, string LastName);
List<User> users = /* ... */

List<string> names = users
    .Map(user =>
    {
        // could be written as a lambda
        // but sometimes you might need more space or add helper variables
        return $"{user.FirstName} {user.LastName}";
    })
    .ToList();
```

## What is `.ToList`?

LINQ operations can be chained. If the result is supposed to be another list,
you can get it by writing `.ToList()` at the end. Alternatively, if you like
arrays, you can write `.ToArray()` at the end of the chain.

Be careful about not writing it! This requires a bit more understanding of how
lazy evaluation works. Be safe -- always write `.ToList()` at the end.

## FirstOrDefault

```csharp
class User
{
    public string Name { get; set; }
    public int Age { get; set; }
}
List<User> users = /* ... */

// also can use var here
User first = users
    .Where(user => user.Age > 40)
    .FirstOrDefault();
if (first is null)
{
    Console.WriteLine("No one is older than 40");
}
else
{
    Console.WriteLine($"{first.Name} is older than 40");
}
```

## Ordering (sorting)

Lists can be sorted by calling `OrderBy` or `OrderByDescending`. Provide a lambda
that extracts the value used for sorting.

```csharp
var sortedOlder = users
    // sort by age, smaller number first
    .OrderBy(user => user.Age)
    // keep only older than 40
    .Where(user => user.Age > 40)
    .ToList();

// now sortedOlder is only those users
```
