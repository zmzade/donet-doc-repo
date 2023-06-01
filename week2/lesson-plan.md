# Week 1 - lesson plan

## Agenda

- Q&A about preparation materials
- Let's take a look at preparation materials (Ivan)
- Let's talk about C# Fundamentals
  - Methods (Ivan)
  - Generics (Ivan)
  - Lists (Calin)

## Exercises

### 1. Caclulator
Make a `GET` endpoint that will take parameter `number1` and `number2` and based on `operation` parameter will perform one of following operations: addition, substraction, multiplication. If number1 or number2 are not a number return bad request response. For operation valid values are `add`, `substract`, `multiplay`.

Example: `GET /calculate?number1=5&number2=10&operation=add` would return 15 as a result.

### 2. Method example
Make a `GET` endpoint that will take `input` parameter. If `input` parameter is an integer call `AddNumbers` method that receives input and returns sum of all integer values. If input is not an integer then call method `CountCapitalLetters` method that receives input and returns counter of all caputal letters.

Hint: use `int.TryParse()` and `char.IsUpper()`

Example 1: `GET /?input=153` would calculate 1 + 5 + 3 and return 9.
Example 2: `GET /?input=The Quick Brown Fox Jumps Over the Lazy Dog` will return 8.


### 3. Distinct alphabetical list
Make a `GET` endpoint that takes a `string` as input and returns a new list containing only the unique characters, sorted in alphabetical order. For example, if the input `string` is `The cool breeze whispered through the trees`, the output should be `["b", "c", "d", "e", "h", "i", "l", "o", "p", "r", "s", "t", "u", "w", "z"]`.

