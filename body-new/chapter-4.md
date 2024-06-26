# Polymorphism

## Key Terms

- Dependency Inversion
- Inversion of Control
- Dependency Injection
- Generics
- Constraints

## Goals

After completing this chapter you will be able to...

Able to understand the difference between Dependency Inversion, Inversion of Control, and Dependency Injection.

Can apply Dependency Inversion at the lowest level of code.

Understands the basic principle of Inversion of Control and how it affects code quality.

Knows how to implement Dependency Injection and why it is so crucial for automated testing.

Grasp the power behind generics and why it allows us to cut down on duplicate code.

Capable of using generics with constraints to unleash powerful paradigms in object-oriented programming.

## Introduction

- Polymorphism is one of the core tenants of Object-Oriented Programming
- Polymorphism can be confusing to understand when first trying to grasp the concept
- Polymorphism is the most powerful of the OOP pillars
- Polymorphism allows us to leverage automated testing in areas where it would be impossible

## Wisdom

"Don't call us. We'll call you." - All of Hollywood. Sugarloaf.

The "hollywood principle" is a well known in the entertainment industry as a standard for how aspiring actors and musicians are treated before they make it big. When a band or actor is young, the agencies and movie studios have all the power. Software has the same relationship with dependencies. Engineers declare what they desire, and they are called when required.

## Simple Success - Inter-modal Container

- The inter-modal container was introduced in the mid part of the 20th century
- It revolutionized how we transport goods around the globe
- A container can fit on a ship, truck, or train
- Greatly speeds up the time to load and unload goods at every part of the journey
- Another example of a common interface, a powerful part of software!

## Code

### The Problem with Relying on Concrete Types

- When I talk about hard implementations or concrete classes, I am mostly referring to those that deal with either persistence or going across a wire.
- Concrete types put you as the developer in a box that does not allow for automated testing
- Concrete types do allow all us to provide a mock as a substitute in testing

---

:large_blue_circle: The domain classes discussed in previous chapters do not apply here because they are only involved with data and functions that operated on said data.

---

#### Concrete Types Are Not Mockable

- Concrete types are unable to be mocked by any unit testing framework

**Figure 5-1**

### The Problem with Improper Dependencies

### The Problem with Bad Dependency Injection

- The following is a list of improper ways in which to either use dependency injection, or claim that a technique is property dependency injection, when in reality it is not.

#### Injecting the Service Container

- The service container itself should never be used or inject outside of the initial setup period. Using it like so is a leaky abstraction. Your application should be unaware the container exists.

```csharp
public class MyService
{
    private readonly IServiceProvider _serviceProvider;

    public class MyService(IServiceProvider serviceProvider)
    {
        _serviceProvider = serviceProvider;
    }

    /// Service Methods below.
}
```

#### Resolving from the Service Container

- We should never be resolving from the container itself. This is going one step beyond the previous warning. It is not our responsibility to call methods from the container. It should remain in the background of our application.

```csharp
public class MyService
{
    private readonly IServiceProvider _serviceProvider;

    public class MyService(IServiceProvider serviceProvider)
    {
        _serviceProvider = serviceProvider;
    }

    public void DoSomething()
    {
        var dependency = _serviceProvider.GetRequiredService<MyType>();
    }
}
```

#### Using Statements as Resource Management

- The purpose of a DI container is to both manage our dependencies and the scopes or life-cycles of those dependencies. This allows us to focus on our application, and leave the details of object creation and destruction to the container. A using statement is an explicit way of doing something that should be implicit.

```csharp
public class MyService
{
    using(var dependency = new MyDependency())
    {
        // Use dependency
    }

    // Resource reclaimed.
}
```

- The other glaring issue with a naked using statement is that our method may potentially be un-testable now if our dependency is a non-pure method.

#### Injection other than the Constructor

- While we have three potential options for dependency injection, constructor injection is the preferred method in all situations due to its simplicity and the ability to preserve encapsulation. Another issue is that most DI frameworks have limited or no support for non-constructor injection. Attempting to use it would cause unnecessary complications to our applications with zero upside.

```csharp
public class MyService
{
    public IMyDependency MyDependency { get; set; }

    // Service Methods below.
}
```

- With property injection we have provided an public setter for our dependency, a bad idea. A constructor is a much better and more encapsulated alternative.

<!-- Extract into other section and add testing example. -->

```csharp
public class MyService
{
    public IMyDependency MyDependency { get; private set; }

    public void MyMethod(IMyDependency myDependency)
    {
        // Implement method.
    }
}
```

- While method inject is more encapsulated than property injection, it is still not as safe as normal constructor injection. Another issue would arise when testing. You should have to ensure your dependency is property initialized before testing other methods. Constructor injection forces your to do so before you can call other method.

#### Using the "FromServices" attribute

In web based applications, it is possible to annotate a dependency in a controller action with the "FromServices" attribute tag. This will tell the container to inject the dependency into the controller action when it is called. This is another example of a leaky abstraction. The application is fully aware of a DI container due to the attribute.

```csharp
[HttpPost]
public IActionResult PostRequest([FromServices] IMyDependency dependency)
{
    // Implement method.
}
```
