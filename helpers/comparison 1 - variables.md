# C# vs Node - variables

## C# is a strongly statically typed language

<table>
<tr>
<th>C#</th>
<th>Javascript</th>
</tr>
<tr>
<td>

```csharp
// these are the same
var count = 4;
int count = 4;

```

</td>
<td>

```javascript
var count = 4;
let count = 4;
```

</td>
</tr>

<tr>
<td>

```csharp
// this is valid, but done rarely
const int max = 3;
const string title = "Dear";
```

</td>
<td>

```javascript
const max = 3;
const title = "Dear";
```

</td>
</tr>

<tr>
<td>

```csharp
// works
double i = 2.3;
i = 1;

// won't work
int i = 1;
i = 2.3;

// Also won't work
var t = 2;
t = "";

```

</td>
<td>

```javascript
let i = 1;
i = 2.3;
i = "text";
```

</td>
</tr>

</table>

## Type conversion

```csharp
// this works since 3 is the same as 3.0
double size = 3;

// doesn't work!
int average = 2.3;

// try one of the following
int average = (int)1.5;             // Prints 1
int average = (int)Math.Floor(1.5); // Prints 1
int average = (int)Math.Round(1.5); // Prints 2
int average = Convert.ToInt32(1.5); // Prints 2
```

Decimal types like `float`, `double` and `decimal` are too big for `int` so they
need to be manually "reduced" to `int` via any of the above functions.
