# Homework

## How to deliver homework 

Open this template repository  https://github.com/HackYourFuture-CPH/dotnet-masterclass and click on ![image](https://user-images.githubusercontent.com/6642037/115988976-3796da80-a5bc-11eb-9184-554a2218b2ae.png) and then create a copy of this structure on your own GitHub profile with the name ``hyf-dotnet-masterclass``

Create a PR to add your homework to the respective week folder like you are used to do in the web development course, and if you don't remember how to do hand in homework using Pull Requests, please check here https://github.com/HackYourFuture-CPH/JavaScript/blob/master/javascript1/week1/homework.md

## Homework exercises for Week #3

1. Create `Account` class that can be initialized with the amount value. Account class contains `Withdraw` and `Deposit` methods and Balance (get only) property.
Make sure that you can't withdraw more than you have in the balance.

```
var account = new Account(100m);
account.Deposit(100);
Console.WriteLine($"Account balance is {account.Balance}");
account.Withdraw(20);
Console.WriteLine($"Account balance is {account.Balance}");
account.Withdraw(200); // ❌ we should not be able to withdraw more than we have in the balance
```

2. Create interface `IAnimal` with property `Name` and `Sound` . Create classes `Cow`, `Cat` and `Dog` that implement `IAnimal` . Instantiate all three classes and pass them to a new method called `MakeSound` that has single parameter `IAnimal` and it writes to console eg “Dog says woof woof” if instance of the Dog class is passed.
