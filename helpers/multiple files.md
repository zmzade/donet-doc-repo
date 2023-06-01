# Working with multiple files

In C#, the typical structure of each file is the following:

```csharp
using System.Collections.Generic;

namespace MyProject.WebApi
{
    /// <summary>
    /// Represents the response of the /api/users endpoint.
    /// </summary>
    public class UsersResponse
    {
        public List<User> Users { get; set; }
    }

    public class User
    {
        public int Id { get; set; }
    }
}
```

Let's analyze it a bit:

- At the top of the file is a list of `using` statements that bring in various helper classes
- Everything in a file is wrapped in a `namespace`. The concept of a `namespace` is used to group classes that belong together.
  - Typically, if your project is called `MyProject` you would put everything in a `MyProject` namespace
  - Namespaces can contain sub-namespaces as seen in the example above

## Modern C#

The same example can be rewritten with namespace as a statement:

```csharp
using System.Collections.Generic;

namespace MyProject.WebApi;

/// <summary>
/// Represents the response of the /api/users endpoint.
/// </summary>
public class UsersResponse
{
    public List<User> Users { get; set; }
}

public class User
{
    public int Id { get; set; }
}
```

This removes one level of indentation and is used in the most recent applications.
Older applications and libraries will still most likely use the structure similar
to the first example.

## Some comments

Q: How many constructs (interfaces, classes, records, structs, enums, delegates) per file?
A: It depends on the style. Typically, we prefer _one_ per file - and the file name matches the object.

For example, interface `IUsers` goes into `IUsers.cs` file and `Users` into `Users.cs`.
But this is not a rule! Sometimes it makes sense to keep both `IUsers` and `Users` in the same file.

Similarly, sometimes we group classes that _always_ go together into a file.

Additionally, file name doesn't have to be the same as the construct. You could have a file
called `VatHelpers.cs` that contains a bunch of things that are related to VAT calculations,
and none of them is actually called `VatHelpers`.

Q: What is the difference between `namespace A.B` and `namespace A`?
A: The first one is "nested".

Namespaces can go inside another and: `namespace A.B` is functionally the same
as:

```csharp
namespace A
{
    namespace B
    {
    }
}
```

Q: What if I have a folder in my project?
A: We tend to use the name of the folder in our namespace.

Consider the following: in your web project the root namespace is `MyProject.Api`
and you create a folder called `Users`. You might put a file named `UsersResponse.cs` in that
folder and then put the following namespace inside `namespace MyProject.Api.Users`.

Again, this is not a hard rule, it is simply a convention.
