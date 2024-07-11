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

- In web based applications, it is possible to annotate a dependency in a controller action with the "FromServices" attribute tag. This will tell the container to inject the dependency into the controller action when it is called. This is another example of a leaky abstraction. The application is fully aware of a DI container due to the attribute.

```csharp
[HttpPost]
public IActionResult PostRequest([FromServices] IMyDependency dependency)
{
    // Implement method.
}
```

#### Injecting Non-Reference Values

- Dependency Injection is a fancy way of implementing the Proxy pattern automatically for you. With the primary purpose of being able to use a different implementation type for automated testing purposes. In almost all cases, we use interfaces or static (singleton) concrete objects for dependency injection.

```csharp
public class MyService
{
    public string _dependency;

    public class MyService(string dependency)
    {
        _dependency = dependency;
    }

    // Methods below here.
}
```

- If the string is for configuration purposes, then you should yield to using the "IConfiguration" interface, or place it in a defined type. Registering individual strings is not allowed in most DI frameworks, or is confusing at best.

#### Registering Dependencies in Abnormal Ways

- Dependencies should only be registered in one of two ways:

1) A single concrete class to a single interface
2) A single concrete class by itself for static configuration values

Some DI frameworks allow fancier ways of registration such as via lambda expressions, conditional injection, property injection, or dynamic registrations. These are all unnecessary and will only complicated the application for yourself and others.

#### Using Service Locators

- Service Locators have a time and place, but that time and place does not involve application level code.

A service locator works in a similar fashion to a DI container, however in many cases you have to explicitly ask for what service you are looking for. This is the antithesis of what DI should be. DI is all about defining what dependencies you need at runtime and the container giving them to your application when needed.

```csharp
public class MyService
{
    private readonly IMyDependency _dependency;

    public MyService(IServiceLocator locator)
    {
        _dependency = locator.FindService<IMyDependency>();
    } 
}
```

- Service locators are leaky abstractions. Your application should be unaware of any hard dependencies. Even though the service locator is in the form of an interface, it still leaks to the application details that the locator exists.

#### Writing Your Own DI Container

- There are much more important things to focus on in your application than worrying about having a customer DI implementation. If you feel that you need to write your own implementation to service a specific purpose-you've lost the plot. It is important to ask yourself why you feel you need to write a custom container, and then see if another solution exists where the default one will satisfy your needs.

#### Modifying or Extending the Existing Container

- In the same vein as above, if you so desire a feature that does not exist in the default implementation ask yourself why are you are seeking to modify the container. Again, there is a high chance this is not required and the default solution will fit your needs just fine.

#### Swallowing Registrations

- When you are registering dependencies, you need to make sure that you are registering implementations with the correct lifespan. Swallowing registrations entails one dependency holding on, or not letting another resource be reclaimed because it has a longer lifespan.

In the following example, class A is a singleton that has a dependency on class B, with a transient lifespan. However, because A will never be reclaimed, B will also never be reclaimed as well.

```csharp
container.RegisterSingleton<ClassA>();
container.RegisterTransient<ClassB>();
```

The code shows that ClassB is never released because ClassA will never be reclaimed.

```csharp
public class ClassA()
{
    // ClassB is now effectively a Singleton because A will never be garbage collected.
    private readonly ClassB _b;

    public ClassA(ClassB B)
    {
        _b = B;
    }
}
```

- Remember when you are defining the lifespans of objects to never let transient objects be dependencies of scoped or singletons, or for transients to be dependencies of scoped objects. This may result in undefined behavior for your application when it expects an object to be reclaimed by the garbage collector when in reality, it stays allocated in memory.

#### Injecting Excessive Dependencies

- There is no hard rule for how many dependencies should or should not be in a class, however I have found that the hard limit is five. More or equal to this and testing becomes annoying very fast.

- Remember that every dependency you inject into a class means one more that must be mocked in an automated test. More than three will be annoying, and more than four will be downright frustrating.

```csharp
public class MyServiceTests
{
    private readonly IFirstDependency _first;
    private readonly ISecondDependency _second;
    private readonly IThirdDependency _third;
    private readonly IFourthDependency _fourth;
    private readonly IFifthDependency _fifth;

    public MyServiceTests()
    {
        _first = new Mock<IFirstDependency>();
        _second = new Mock<ISecondDependency>();
        _third = new Mock<IThirdDependency>();
        _fourth = new Mock<IFourthDependency>();
        _fifth = new Mock<IFifthDependency>();
    }
}
```

- The unit test above shows the boilerplate required just to to get the test wired up and working. This is not code that you will want to maintain long-term as the tests associated with this kind of excessive code tent to be brittle and break easily due to the high number of logical branches involved.

#### Null Checks on Injected Services

- When you run your application, the container will run a series of checks to ensure that everything has been wired up correctly. An exception will be thrown in the case that you have missed something.

```csharp
public class MyService
{
    private readonly IMyDependency? _dependency;

    public MyService(IMyDependency? dependency)
    {
        _dependency = dependency ?? throw new ArgumentNullException(nameof(dependency));
    }
}
```

- Checking injected services for null is a bad paradigm because you are creating two logical branches every time the object is dereferenced. This will lead to lots of unnecessary automated tests that serve no purpose.

- Do not try to silence the complier in your code with the "!" operator. This will lead to undefined behavior in edge cases when the reference actually is null. The best way to avoid nullable values is not to have them in the first place. The DI container will ensure that all dependencies are valid at runtime or it will warn you with an exception when your application launches.
