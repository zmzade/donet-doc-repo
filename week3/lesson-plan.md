# Week 3 : Types, classes and interfaces

## Pre-requisites:

- Went through preparation

## Lesson Plan:

1. Questions from preparation and homework from week 2
2. Let's go over the preparation materials
3. Exercises!

## Exercises

1. Create a class named `Temperature` and make a constructor with one decimal parameter - degrees (Celsius) and assign its value to Property. The property has a public getter and private setter. The property setter throws an exception if temperature is less than 273.15 Celsius. Then, create a property `Fahrenheit` that will convert Celsius to Fahrenheit (it has no setter similar to `NicePrintout`)

Bonus: create Kelvin property

```
var temperature = new Temperature(10);
Console.WriteLine($"{temperature.Celsius} Celsius is {temperature.Fahrenheit} Fahrenheit");
```

2. Create a class named `ExchangeRate` with a constructor with two string parameters, `fromCurrency` and `toCurrency`. Add a decimal property called `Rate` and method `Calculate` with decimal parameter `amount` return value of the method should be a product of `Rate` and `amount` multiplication.

Bonus: We should also check that `Rate` or `amount` are not negative.

```
var amount = 100;
var exchangeRate = new ExchangeRate("EUR", "DKK");
exchangeRate.Rate = 7.5m;
Console.WriteLine($"{amount} {exchangeRate.FromCurrency} is {exchangeRate.Calculate(amount)} {exchangeRate.ToCurrency}");
```
