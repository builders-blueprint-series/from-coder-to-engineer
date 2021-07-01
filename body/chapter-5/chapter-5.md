# Chapter 5

## Patterns To Remember

After completing this chapter, you will be able to

- Understand the purpose of design patterns

- Recognize that all patterns have a time and place

- Be able to recognize when to use design patterns in your applications

- Learn how patterns can be used together to create well engineered software

- Find how to find a common interface between software components

- Implement specific patterns into your software

## Introduction

1. Design patterns are commonly associated with the GOF book published in the early 1990's

2. Other patterns exist in software besides the commonly published GOF patterns

3. Patterns are useful because they are tried and true pieces of software engineering that have stood for decades

4. Understanding where and when to use a pattern is the hardest and most important part

5. This book tried to focus on behavior patterns over others

## The Purpose of Design Patterns

1. Design patterns are there to make your software life easy

2. All patterns have a time and place where they are appropriate

3. It is your job as a software engineering to learn and recognize when to use a pattern

4. When used properly design patterns are powerful tools that turn good software into great software

### There are no such things as anti-patterns

A narrative that is echoed by some people in the software industry is that certain patterns out there are "anti-patterns". In that they hold a net negative effect on ones software. Many people imply that these "anti-patterns" should never be used under any situation. Many times I have heard a co-worker at work blurt out the phrase, "Singleton is an anti-pattern." Or, "Service Locator is an anti-pattern." It is unfortunate that such an opinion exists in our industry.

I would like to present a different opinion. There is no such thing as an anti-pattern. Only an improperly applied one.

For example, if your application uses a Http client, WCF channel factories, system clock, or gRPC channel. These are typically registered as singletons in a dependency injection framework. Just because you are not writing your own singleton class for these objects does not mean you are not using the pattern. Your dependency injection framework or IOC containing is using the pattern from which you get to benefit from.

As for Service Locators, every dependency injection framework or IOC container is also using a service locator, or relying on a service locator to resolve objects when they are needed. I have yet to see a blog article argue against dependency injection, so it would be rather hypocritical to argue against service locators, but support an IOC container.

Every pattern have a time and place when it can and SHOULD be applied. For some patterns, this window of opportunity is very small, for others it is abundant. Remember, as the curator of your application, only you know what is truly appropriate. Just because certain pattern may be a good or bad fit for someone elses application does not mean that it is equally good or bad for yours as well.

This notion goes beyond software anti-patterns, do an internet search for the term "anti-pattern" that you will come back with blog article after blog article of "Product Owner Anti-Patterns", "Scrum Anti-Patterns", and "Dev Ops Anti-Patterns". Some people event assert the notion that design pattern in and of themselves are an anti-pattern. Again, people do not take this absolutism by others as a software dogma that be be adhered to at every possible opportunity. All software applications are different, and each needs to satisfy different requirements.

## Learning Where To Apply Patterns

## Finding A Common Interface

### Why We Want Common Interfaces

### Real Life Common Interfaces

There are many non-software engineering examples, where less is more. Simplicity is removing options, not providing them. Some of the most successful and respected companies operate by offering their customers very few options all.

In-N-Out burgers is a fast-food chain that originated in Baldwin Park, California in 1948. Their original menu consisted of hamburgers, cheeseburgers, fries, and soft drinks. The menu has changed very little since their inception. This is in contrast to other fast-food chains such as McDonald's or TacoBell which experience a constant churn of product changes, updates, additions, and removals. That is because In-N-Out is not a fast-food company, they are an "experience" company. When you go to any In-N-Out, everything is exactly the same. Their stores all have the same layout and design. Most customers can probably recite the menu from heart because it has so many options. And every order you make is consistent from any other store. The process of going to In-N-Out, from the moment you walk in the door, until you walk out is exactly the same. In-N-Out sells consistency, not hamburgers.

Costco Wholesale is a big-box retail store that was founded in 1994 just outside of Seattle. While Costco sells thousands of items in their store and online, each item they sale typically has very few options available to choose from. If you want to buy ketchup or napkins from Costco, you typically have one name brand and possible a generic brand to choose from. This is intentional on Costco's part because human behavior has a tendency to succumb to analysis paralysis. When people are presented too many options to choose from, they end up not choosing any of them because they are overwhelmed with options. For Costco, this means a loss of possible sales if people aren't buying products from them. Limiting options to customers ends up helping Costco in two ways; it forces customers to buy products, and it helps move product faster, which cuts down on costs.

Southwest Airlines was founded in 1967 as an exclusive inter-state airline that only flew inside the state of Texas for the first part of its existence. Today Southwest is the largest domestic airlines in the United States. The foundation for Southwest's success has always been by offering a limited set of options to its' customers. The airline only flies one type of airplanes, has a single class of service and does not serve meals. In addition to a straight forward product, the airline offers a simple fare structure that contains very few restrictions, rules, or extra fees attached to a ticket. By doing so, the airline offers every customers with a bundled solution that contains all the necessary ways to get from one location to another with very little hassle. By forgoing products that only a small subset of its customers would use, lounges, business class, meals, or seat assignments; Southwest has allowed more
people to fly by offering far less than most other carriers.

Great software functions the exact same way as these companies, it finds a way to only offer what is absolutely necessary. It purposely restricts options to both end-users, and other engineers so that the correct decision in many cases is the only decision available.

### Common Interface Examples

Existing examples of common interfaces in software are both ubiquitous, but rarely explained in computed science programs or software engineering books. Being able to identity and create common interfaces in software is a skill that must be learned over time. This books goal is to train to mind to look and think in terms of find common interfaces. As the master of your own application, it is your job to continuously explore and discover possible ways to condense code and exact a common interface.

#### IEnumerable<T>

The most commonly used common interface is the IEnumerable<T> interface from the generic collections namespace. And the reason it is so useful is probably not what automatically comes to mind. You might be thinking that IEnumerable is a useful common interface because it can hold a List, Dictionary, or HashSet. And yes, that is a very good reason for why IEnumerable is a wonderful interface, but it is not what I want to talk about here. IEnumerable is a great interface because it can hold n number of value of reference types. In math, n is a a variable which simply means any number of items in a collection. IEnumerables' greatest value is that you have no clue how many items it contains; and even better, it does not matter from a code standpoint.

Let's step back for a moment and get a little abstract. Imagine you are counting the number of towels in your household. You place all of the towels on the floor in front of you and start counting. How could we represent this in code?

```csharp
const int myTowels = 3;
```

There is not wrong with this approach, but it only represents the state of our towel ownership in one and exactly one possible state. We could own eleven towels.

```csharp
const int myTowels = 11;
```

Or maybe we own a super fancy ceiling dryer which means we don't need to own any towels at all.

```csharp
const int myTowels = 0;
```

As you can see, our state of towel ownership is in three very different states, which severely limits our possibilities. What if we wanted to add a towel to our ownership.

```csharp
myTowels++;
// Compiler error, myTowels is a constant.
```

Therein lies the problem, we want values single values to be consistent and never change, or be able to change dynamically without us knowing the details of the how, when, and why. What we want, is for our variable to be an IEumerable, so the value is inconsequential.

```csharp
IEnumerable<int> myTowels = new List<int>{ 3 };
```

We still own three towels, but we have encapsulated our ownership from a hard coded variable, to a dynamic object that gives us a interface to work with. IEnumerable is actually a rather boring interface, it only has one method. Luckily for us, that's all we need. The GetEnumerator() will return an iterator that will allow to go sequentially go through the entire collection from the start until the end. THIS is the real power of IEnumerable, the keywords here are from the start and end. We can now write code that is collection length agnostic. Let me repeat that. We can write code that is indifferent to how many towels we own.

In short: IEnumerable + foreach = software awesomeness.

**Listing 9-X** Foreach loop over enumerable

```csharp
IEnumerable<int> myTowels = new List<int>();

foreach(var towel in myTowels)
{
    DryTowel(towel);
}

```

Here we created an empty list, and called the DryTowel method inside of the a foreach loop. But since we our list had zero towels, the method was never called. Had we had 8,213 towels in our list, the method would have been called 8,213 times.

<!-- Do something with LINQ/Functional here -->

#### Generic Delegates

One way you can create a common interface in your application is merging generic delegates into a single Func. In C#, we have three generic delegates available to use, Func, Action, and Predicate.

In keeping with our reservation system, let's imagine we have a class that contains a list of reservations with three possible methods for interacting with the data. This class is unique in that it contains three functions which will accept a generic delegate that allows an end-user to choose how they will query and interact with the data.

**Listing 9-X** Reservation Service

```csharp
public class ReservationService
{
    private readonly IEnumerable<Reservation> _reservations;

    public ReservationService(IReservationRepository reservationRepository)
    {
        _reservations = reservationRepository.GetAllReservations();
    }

    public T Interact<T>(Func<IEnumerable<Reservation>, T> func)
    {
        return func.Invoke(_reservations);
    }

    public bool Interact(Predicate<IEnumerable<Reservation>> predicate)
    {
        return predicate.Invoke(_reservations);
    }

    public void Interact(Action<IEnumerable<Reservation>> action)
    {
        action.Invoke(_reservations);
    }
}
```

In the class above, we have a Reservation Service that allows a person to supply a number of generic delegates to the class, which will invoke them and return a result. For this class, because the delegates are being supplied to us, the class does not require any unit tests. However any delegates you passed would need to be unit tested. We like the ability for the user to supply the function used, but we don't like having to support three different options. There has to be a way to take the Interact function and go from multiple overloads, to a single method.

With this class we could pass in the following functions:

```csharp
Func<IEnumerable<Reservation>, IEnumerable<Reservation>> TodaysReservations = (x) => x.Where(y => y.DateTime == DateTime.Today);

Predicate<IEnumerable<Reservation>> RestaurantIsFull = (x) => x.Count() == 100;

Action<IEnumerable<Reservation>> CloseOldReservations = (x) => x.Where(y => y.DateTime <= DateTime.Today).ToList().ForEach(y => y.Close());
```

These three generic function above can be passed to our reservation class Interact method, and the overloaded method will return, or in one instance, not return the result.

**Listing 9-X** Generic delegate signatures

```csharp
public delegate TResult Func<in T, out TResult>(T obj); - Takes in a type of T, and returns a type of TResult.

public delegate void Action<in T>(T obj); - Takes in a type of T, and returns void.

public delegate bool Predicate<in T>(T obj); - Takes in a type of T, and returns a boolean.
```

Above are the three main generic signatures for generic delegates in C#. Note that Func can be overloaded to take up to sixteen different inputs.

How we go go from three different signatures, to just one?

The first move is easy. We can just avoid using Predicate all together. When we want to return a boolean from a generic delegate, we just use a Func with a boolean return type.

**Listing 9-X** Func with boolean return

```csharp
Func<int, boolean> isEvenNumber = (x) => x % 2 == 0;
```

Here we have defined a Func that will tell us if a number is even or not. By explicitly using a Func instead of Predicate, we no longer need to worry about having to support another interface.

The next part of combining Action and Func into a single interface is a little harder. We can deduce that Func will be the nominal survival because we need to have the ability to support return types. Not everything can be void, but we can't just use void as a return type in a Func.

**Listing 9-X** Attempt to return void from Func

```csharp
Func<int, void> notPossible = (x) => Console.WriteLine(x);
// Compiler error. CS1547: Keyword 'void' cannot be used in this context
```

If we look at the Func signature, we need to supply the generic with an object instead of a type keyword. We need a class that represents void. If you are not familiar with this concept, it is a functional programming paradigm. In many functional language, the term "Unit" is a first class citizen used to represent void.

**Listing 9-X** Unit class

```csharp
public class Unit
{
    public static Unit Value { get; } = default;
}
```

The Unit class above has a single property called Value that will return a null instance of the Unit class. We can now apply our Unit class to our Func as follows.

**Listing 9-X** Func that returns a Unit

```csharp
Func<int, Unit> possible = (x) =>
{
    Console.WriteLine(x);
    return Unit.Value;
}
```

We now have a generic function that "returns" void in the form of a Unit class. While the end result is not quite as compact as we wish, the functionality we get from having a common interface can replace three interfaces far outweighs the negative impact of another couple lines of code.

Lets see an example of how condensing all of these generic interfaces into a single common interface can have a positive impact on our code.

**Listing 9-X** Reservation Service with only one method

```csharp
public class ReservationService
{
    private readonly IEnumerable<Reservation> _reservations;

    public ReservationService(IReservationRepository reservationRepository)
    {
        _reservations = reservationRepository.GetAllReservations();
    }

    public T Interact<T>(Func<IEnumerable<Reservation>, T> func)
    {
        return func.Invoke(_reservations);
    }
}
```

Our final class removed the option to pass a Predicate or Action because we learned how we can use a Func to pass a generic delegate that can return a boolean, void, or any other type for that matter.

**Listing 9-X** Updated generic functions

```csharp
Func<IEnumerable<Reservation>, IEnumerable<Reservation>> TodaysReservations = (x) => x.Where(y => y.DateTime == DateTime.Today);

Func<IEnumerable<Reservation>, bool> RestaurantIsFull = (x) => x.Count() == 100;

Func<IEnumerable<Reservation>, Unit> CloseOldReservations = (x) =>
{
    x.Where(y => y.DateTime <= DateTime.Today).ToList().ForEach(y => y.Close());
    return Unit.Value;
};
```

With small modifications, we have changed all three of our generic functions to all use a Func, and can now reap the benefits of a common interface.

However, we are not quite finished. Now that we have a single interface for our Reservation Service, we can apply this to all other classes in our application. What if we were doing something similar in a Customer class. We could extract the Interact method as follows.

**Listing 9-X** Interact interface

```csharp
public interface IInteract
{
    TResult Interact<T, TResult>(Func<IEnumerable<T>, TResult> func);
}
```

**Listing 9-X** Reservation Service implements new interface

```csharp

```

##### Other Instances

Keep in mind that there are many instances of using an interface with the greatest common denominator and then passing in only the values that are necessary. You will see in a little bit the Mediator pattern will use what we just learned to great effect.

#### Factorials

## Patterns In Detail

### Creational Patterns

### Structural Patterns

### Behavioral Patterns
