# Week 5 : Dependency injection and working with persisted data

## Pre-requisites:

Having a finished week 4 homework.

## Agenda

1. Q&A - 15m
2. Dependency injection - 20m
3. Data persistence - 20m

---

5. Break - 30m

---

## Exercise:

1. Create a new web application (`dotnet new web`) inside `mealsharing` folder and start by creating a Meal class. Add relevant properties:
   - Headline
   - image url
   - body text,
   - location
   - price

Create an `IMealService` interface with the following methods:

- `List<Meal> ListMeals`
- `AddMeal(Meal meal)` methods.

Create a `FileMealService` class that implements the `IMealService` interface:

- `ListMeals()` method - reads and deserializes meals from the file
- `AddMeal` method - save (persist) meals to a file. **Remember:** call `ListMeals` to read all meals before adding new meal to the list.

Register `FileMealService` as service using dependency injection.

```csharp
builder.Services.AddScoped<ISomething, Something>();
```

Create a simple API for adding and listing meals using the MealService.

```csharp
app.MapGet("/meals", ([FromServices] IMealSharingService mealSharingService) => { ... });
app.MapPost("/meals", ([FromServices] IMealSharingService mealSharingService, Meal meal) => { ... });
```

Run the appplication, add and list meals. Try stopping and runnign application again.
