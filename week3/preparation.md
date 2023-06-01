# Week 3 - Types, interfaces, classes

We mentioned before that C# is a strongly typed language. This means that at every point, the compiler knows the type of each variable. Often times, we want to group several variables and move them together.

In JavaScript, you could do the following:

```jsx
const person = { name: "Ash" };
console.log(person.name);
person.name = "Mauve";
```

In C# you can do the the following:

```csharp
var person = new { Name = "Ash" };

// This doesn't work!
person.Name = "Mauve";
```

So, how do we group variables into a larger unit? The answer is classes.

## Classes

Let’s take a look at a very simple class:

```csharp
// You can use any of the following:
var ash = new Person();
var mauve = new Person { Name = "Mauve" };
Person xavier = new Person() { Name = "Xavier", Id = 3 };
Person santos = new Person
{
    Id = 1,
    Name = "Santos"
};

public class Person
{
	public int Id { get; set; }
	public string Name { get; set; }
  public string AddressLine { get; set; }
  public string City { get; set; }
}
```

Classes contain properties, fields and methods. In the above example, `Id`, `Name`, `AddressLine` and `City` are all properties. You have already used some classes before like `List<T>` or `WebApplication` in the minimal API code.

🏗️ **Task:** Try running the code above. Then

- Add a new property `Country` of type `string`
- Create a variable of type `List<Person>` and add some instances of class `Person`to the list

### Constructors

Sometimes you want to **\*\***ensure**\*\*** certain properties are set when you create a new instance. For that we will use constructors. They are special “methods” that have the same name as the class and no return type. Example:

```csharp
// the following no longer works
var person = new Person();
// instead, write
var xavier = new Person(1, "Xavier");

public class Person
{
  public int Id { get; set; }
  public string Name { get; set; }

  public Person(int id, string name)
	{
		Id = id;
		Name = name;
	}
}
```

We want to use constructors whenever a part of the functionality is **\*\*\*\***required**\*\*\*\*** and without which the instances would be broken.

### Fields

Classes can have fields in them. They are similar to regular variables and are often times prefixed with an underscore. Let’s take a look at an example:

```csharp
public class Project
{
  // fields can be public, but oftentimes are not!
  private int _id;

  public string Name { get; set; }
}
```

The syntax for fields are similar to regular variables.

```csharp
var project = new Project
{
  Name = "My first project",
  // works if fields are public
  //_id = 1
};
```

Fields are _typically_ `private` while properties are typically `public`. Think of fields as internal state that is hidden while properties are intentionally exposed to whomever.

### Properties

Let’s talk a bit more about properties - they have a bit strange syntax.

```csharp
public class Repository
{
  public string Id { get; private set; }
  public string Name { get; private set; }

  public Repository(string id, string name)
  {
		Id = id;
    Name = name;
  }
}
```

Oops, `Name` has a private setter! Private setter means that only the internal parts of the class can change the values! No one on the outside can!

```csharp
var repo = new Repository("123", "hack-your-future/dotnet-masterclass");
// works since get is public
Console.WriteLine(repo.Id);
// doesn't work!
repo.Id = "456";
```

Why do something like this? This is used to _protect_ the internal representation and to ensure that at no point in the program this can be altered. Hiding implementation is a key tenet of object oriented programming.

Unlike fields, properties can have public read access, while private write access. Fields must either be completely public or completely private.

Let’s explore properties a bit more:

```csharp
public class ValidUserRegistration
{
    // Auto property - public read and write
    public string Comment { get; set; }

    // Auto property - public read, private write
    // Note that we provided a default value too!
    public string Note { get; private set; } = "-";

    // Auto property - only settable in constructor
    public int Id { get; }

    public ValidUserRegistration(int id)
    {
        // this only works here
        Id = id;
    }
}
```

Note that you can put any access modifier before `get` and `set` - if that makes sense for your scenario. You _must_ have a getter, but setter is optional. If a setter is not provided, only constructor can set the value.

Getters and setters are actually functions! Let’s write them expanded:

```csharp
public class UserEntry
{
    // this is reffered to as a backing field
    private string _name;

    // get and set are methods that can be written in a short format
    public string Name
    {
        get => _name;
        set => _name = value;
    }

    // Default value is provided for this field!
    private DateTime _expiresOn = new DateTime(1970, 1, 1);

    public DateTime ExpiresOn
    {
        get
        {
            return _expiresOn;
        }
        set
        {
            _expiresOn = value;
        }
    }
}
```

Obviously, having a backing field expands the code significantly, it’s better to have a shorter version instead.

However, having the ability to write getters and setters provides additional benefits: validation and computation. Let’s take a look at an example:

```csharp
public class UserEntry2
{
    // get and set are methods that can be written in a short format
    public string Name { get; set; }

    // Default value is provided for this field!
    private DateTime _expiresOn = new DateTime(1970, 1, 1);

    public DateTime ExpiresOn
    {
        // just return a value
        get => _expiresOn;
        set
        {
            if (value < DateTime.Today)
            {
                throw new Exception("Ouch! It cannot expire in the past");
            }
            _expiresOn = value;
        }
    }

    public string NicePrintout
    {
				// compute a value, there is no backing field
        get => $"{Name} - expires on {ExpiresOn}";
    }
}

var entry = new UserEntry2 { Name = "Homework 3" };
// you can use helper methods to get a relative date
entry.ExpiresOn = DateTime.Today.AddDays(10);

var yesterday = DateTime.Today.AddDays(-1);
entry.ExpiresOn = yesterday; // KABOOM 💣
```

We use `throw new Exception()` whenever we want to signal to the caller that something bad has happened. It is similar to `throw` in Javascript - but unlike Javascript, what you throw _must_ be a valid class inheriting from an `Exception` like `ArgumentException`, `InvalidOperationException`, etc.

Phew, let’s recap!

- Properties represent state
- They have getters and optional setters
- They can be initialized in constructor
  - If private, they can only be set in other properties, methods or constructor
  - If no setter, only in constructor
- They can have a default value
- Getters and setters are actually methods! When fully expanded, you can write anything. The only requirement is that a getter must return a value. Setters don’t have to return anything, they are considered `void`

### Methods

Fields are internal state, properties are public state that protects changes. Methods add functionality to instances. Another way of looking at it: properties are nouns, methods are verbs.

```csharp
public class Stats
{
    public string Key { get; set; }
    public List<int> Values { get; } = new List<int>();

    // short form
    public void AddValue(int value) => Values.Add(value);

    public decimal GetAverage()
    {
        if (Values.Count == 0) return 0;

        var sum = 0;
        foreach (var value in Values) sum += value;
        return sum / Values.Count;
    }
}
```

Each method has

- an access modifier (`private` methods are only available to other methods and properties in that class)
- a return type (`void` for nothing to return)
- a name, usually with capital first letter
- parameters
- local function(s)

It can be written in a short form (arrow syntax for one liners) or as a block

## Interfaces

Once you start writing classes, you will write a lot of them! When writing library logic, helper functions or anything that works across many classes, you will need to abstract it. Consider the following example:

```csharp
public class Renderer
{
    public string Render(List<User> users)
    {
        // same as js
        // users.map(user => user.Id).join(", ");
        return string.Join(", ", users.Select(user => user.Id));
    }

    // this is silly
    public string Render(List<Company> companies)
        =>  string.Join(", ", companies.Select(company => company.Id));
}
```

You might be clever and think _generics_!

```csharp
// you might be clever...
public string Render<T>(List<T> items)
{
    // but it won't work!
    // T doesn't contain property Id
    return string.Join(", ", items.Select(item => item.Id));
}
```

But sadly, it won’t work. We mentioned that C# is strongly typed and that compiler needs to know what T is and has - and sadly, compiler cannot guarantee that `T` will contain property `Id`. What if you pass `int` or `string` or `List<List<int>>`even!

We need to somehow explain the concept of “having Id of type `int`" in the `Render` method. And we can do that! By using `interfaces`. Think of interfaces as slim versions of class without modifiers and implementation. Let’s write one:

```csharp
public interface IRenderable
{
    // we want a property named Id that has a getter
    // we don't care about setter, it can be there
    int Id { get; }
}
```

Interfaces typically start with a letter `I` to indicate that they are an interface. As you can see, no access modifier - public is implied. Now we need to ensure `User` and `Company` classes _implement_ the interface:

```csharp
public class User : IRenderable
{
    public int Id { get; set; }
}

public class Company : IRenderable
{
    public int Id { get; set; }
}
```

A class can have many interfaces, not just one. They are comma separated and placed after colon. Let’s see how we can simplify our method:

```csharp
public class Renderer
{
    public string Render(List<IRenderable> items)
        => string.Join(", ", items.Select(item => item.Id));
}
```

We gave a hint to the compiler: expect a list of things that for sure have `IRenderable` shape. Anything that is written in that interface is fair game. Since we specified the property `Id` of type `int`, the code can actually use it!

We will use interfaces quite a lot in web applications as they can simplify testing and allow multiple implementations of the same interface - very useful for scenarios where multiple versions of code look the same from the outside. An interface is a contract that is exposed publicly.

Some popular interfaces:

- `IEnumerable<T>` - represents a concept of “many things”. Arrays, lists and almost all collections implement this interface. This allows us to use `foreach` and traverse through the list. Use this when you just want to go through the list and do something with the elements, but the list will be left unchanged.
- `IList<T>` - interface implemented by `List<T>`. It is actually same as `IEnumerable<T>` but it has more: methods like `Add` or `Remove` and many others. Use this when you don’t care what kind of list it is, but you want to manipulate the contents.
- `ILogger<T>` and `ILogger` - used commonly in ASP.NET. Contains method to log information, warnings, errors, exceptions and more. It is better than `Console.WriteLine` because it can be configured to:
  - Write to a console
  - Write to a file
  - Send data to a HTTP endpoint (like your company logger)
  - All of the above at the same time
- `IDisposable` - common in UI code and in some situations. Powers `using` keyword and allows the implementation to know when the object is no longer being used.

### A quick example

This a common way of writing backend code with ASP.NET:

```csharp
public class User { public int Id { get; } }

public interface IUserRepository
{
    User CreateUser();
    User GetUser(int id);
    void DeleteUser(int id);
}

public class UserRepository : IUserRepository
{
    // implementation doesn't matter now!
    public User CreateUser()
    {
        throw new NotImplementedException();
    }

    public void DeleteUser(int id)
    {
        throw new NotImplementedException();
    }

    public User GetUser(int id)
    {
        throw new NotImplementedException();
    }
}
```

You might wonder - what is the benefit of this? Why not just have a class and use it.

Think abstraction and knowledge. `IUserRepository` is a recipe for how things should work for the consumer - they don’t specify if this is a real database, is it Mongo or MySQL, is it from a file, or is it randomly generated. It also enables multiple providers for the interface and the consumer can just pretend that the implementation will be provided later.

Abstraction is a essential part of programming regardless if you are writing object oriented, procedural or functional code.

## Links

- [Objects and classes](<[https://www.youtube.com/watch?v=TzgxcAiHCWA&list=PLdo4fOcmZ0oVxKLQCHpiUWun7vlJJvUiN&index=16](https://www.youtube.com/watch?v=TzgxcAiHCWA&list=PLdo4fOcmZ0oVxKLQCHpiUWun7vlJJvUiN&index=16)>)
- [Methods and Members](<[https://www.youtube.com/watch?v=xLhm3bEG__c&list=PLdo4fOcmZ0oVxKLQCHpiUWun7vlJJvUiN&index=17](https://www.youtube.com/watch?v=xLhm3bEG__c&list=PLdo4fOcmZ0oVxKLQCHpiUWun7vlJJvUiN&index=17)>)
- [Methods and Exceptions](<[https://www.youtube.com/watch?v=8YsoBBiVVzQ&list=PLdo4fOcmZ0oVxKLQCHpiUWun7vlJJvUiN&index=18](https://www.youtube.com/watch?v=8YsoBBiVVzQ&list=PLdo4fOcmZ0oVxKLQCHpiUWun7vlJJvUiN&index=18)>)
