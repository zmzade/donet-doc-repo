# Week 4 - lesson plan: Async and await

## Agenda

1. Q&A - 15m
2. Kahoot - 15m
3. Introduce async await - 20m
4. Showcase a `asynchronous` http call using `GetAsync` (`https://reqres.in/api/users?delay=1`) - 20m

---

5. Break - 30m

---

6. Introduce Task - 20m
7. Showcase `missing` to await a task. E.g.: Create a loop and call an endpoint with console log => make it throw and show that the execution continues and it fails silently - 20m
8. Showcase usage of `Task.WhenAll` and benefits (posible to evidentiate differnece by using a stopwatch) - 20m
9. (Optional based on time) Extract the logic in an interface + implementation - 20m
10. Go through exercises - rest

## Exercise:

Check https://reqres.in/ and implement

- a get method that gets a response from an endpoint
- a post method that posts to an endpoint e.g. `/api/users`
