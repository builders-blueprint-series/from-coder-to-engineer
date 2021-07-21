# Chapter 8

## Crush Coupling

### After completing this chapter, you will be able to

---

- Understand how coupling hurts application development

- Understand how coupling inhibits testing

- Know when and where to apply inheritance

- Learn how to reduce coupling by avoid certain language features

- Implement dependency inversion the correct way

 > "I love the idea of you." - (Girl friend-zoning guy) Illustration

Software offers us different ways to couple objects together. Concrete, abstract, and interface types are the most common choices we have to implement coupling. Choosing the correct implementation is both a skill and art that is learned over time. Each method has certain advantages and disadvantages that must be weighed. Too much coupling can hinder application development and make future changes difficult. Too abstract, and we lose required details. We prefer to be as abstract as possible without making large sacrifices. In this chapter we will learn to how balance coupling with development requirements. We will understand why it is best to avoid certain language features related to coupling. We will apply best practices for interfaces to keep testing easy and simple. Finally, we will leverage the power of polymorphism to implement dependency injection.

### The "new" Keyword

---

All applications need to deal with creating and destroying objects to implement core features. In most modern languages, the "new" keyword is synonymous with object creation. The use and placement of the keyword is just as important as the objects being created. When we use the "new" keyword to create an object, we are taking on the responsibility of attaching ourselves to the object for the duration of its' lifespan. We are married to it until death. In certain situations this is not an issue, we welcome the situation, others not so much. It is important that you know where the "new" keyword should and should not be. The general rule is that "new" belongs in factories and factory methods. There are some exceptions to that rule which we will go over such as domain events and request objects. Misuse of the keyword will result in code that is hard or impossible to unit test.

#### Using "new" in the Presentation Layer

The presentation layer is a broad description that can describe a number of possibilities such as:

- Rest API
- MVC Controller
- gRPC Service
- GraphQL Handler
- SOA Service
- Console Interface

All of these protocols deal with "presenting" a result from the application in the form of json, xml, stream, or html result. They represent a barrier between your application and a presentation platform such as a mobile or web application. The following examples show a REST API for the sake of simplicity. Please keep in the mind that all of these protocols are implementation details. The same principle applies for all of them.

##### New For Services

Our presentation layer has two main responsibilities:

1) Accepting a request

2) Producing a response for said request

We usually associate other responsibilities as well such as validation and exception handling depending on the scenario. The following example is a "GET" request and does not pass an object in the request body to be processed. Our example is responsible to returning all current reservations in our system. The presentation layer will call the appropriate method and return an http response.

**Figure 8-X** Reservation Controller

```csharp
    [ApiController]
    [Route("api/[controller]")]
    public class ReservationController : ControllerBase
    {
        private readonly IReservationService _reservationService;

        public ReservationController()
        {
            _reservationService = new ReservationService();
        }

        [HttpGet]
        public IActionResult GetAllReservations()
        {
            try
            {
                var result = _reservationService.FindAllReservations();

                return Ok(result);
            }
            catch (Exception exception)
            {
                return BadRequest(exception.Message);
            }
        }
    }
```

Our Reservation Controller has a method to return every reservation. We also declared a dependency on our ReservationService as a private property. The service is initialized in the constructor. The issue is that we are unable to unit any method in our controller because we are married to the concrete implementation of our ReservationService.

**Figure 8-X** Trying to test the GetAllReservationsMethod

```csharp
    [TestClass]
    public class ReservationControllerTests
    {
        private ReservationController _reservationController;

        public ReservationControllerTests()
        {
            _reservationController = new ReservationController();
        }

        [TestMethod]
        public void GetAllReservation_OnSuccess_DoesNotThrowException()
        {
            // Our test code.
        }
    }
```

Our test code highlights a glaring issue with our controller. With our current implementation every test would be connecting to our production level infrastructure and persistence. We do not want test code touching our production environment. We need a way to choose what version of our IReservationService we use at run-time. We want our test code to use a version of the service that does not interact with our persistance or any other communication protocol that may go over a network such as SMTP or a service bus. Imagine the scenario of us testing a feature to email customers when they make a new reservation. With the test coupled to our implementation code every test would email all of our customers. This is not the intended behavior of our test suite. The fix for this is very simple. Instead of declaring an implementation of our IReservationService we will use in the constructor body, we pass it as a parameter.

##### Dependency Injection To The Rescue

Lets look at a stripped down version of our ReservationController:

**Figure 8-X** Stripped down ReservationController

```csharp
    public class ReservationController : ControllerBase
    {
        private readonly IReservationService _reservationService;

        public ReservationController()
        {
            _reservationService = new ReservationService();
        }
    }
```

Let's make a small revision to modify our constructor by accepting an interface as a parameter:

**Figure 8-X** ReservationController now accepts an interface

```csharp
    public class ReservationController : ControllerBase
    {
        private readonly IReservationService _reservationService;

        public ReservationController(IReservationService reservationService)
        {
            _reservationService = reservationService;
        }
    }
```

This tiny change unlocks the potential of polymorphism and all the accompanying benefits.

For reference, our full controller now becomes:

**Figure 8-X** ReservationController with injected interface

```csharp
    [ApiController]
    [Route("api/[controller]")]
    public class ReservationController : ControllerBase
    {
        private readonly IReservationService _reservationService;

        public ReservationController(IReservationService reservationService)
        {
            _reservationService = reservationService;
        }

        [HttpGet]
        public IActionResult GetAllReservations()
        {
            try
            {
                var result = _reservationService.FindAllReservations();

                return Ok(result);
            }
            catch (Exception exception)
            {
                return BadRequest(exception.Message);
            }
        }
    }
```

And our test class can now be updated to accept a mocked version of our IReservationService.

**Figure 8-X** Updated Reservation Controller Tests

```csharp
    [TestClass]
    public class ReservationControllerTests
    {
        private ReservationController _reservationController;

        public ReservationControllerTests()
        {
            var mockService = new Mock<IReservationService>();

            // Setup mock.

            _reservationController = new ReservationController(mockService.Object);
        }

        [TestMethod]
        public void GetAllReservation_OnSuccess_DoesNotThrowException()
        {
            // Our test code.
        }
    }
```

Our mock IReservationService can be initialized to return the desired response depending on the scenario we want to test. With the old implementation, we would only be able to test a single path. And that single path would be going through the implementation details of our infrastructure and persistence layers. The small change of moving the creation of the service from the constructor to a parameter enabled proper unit testing for our controller.

> Info :large_blue_circle:
>
> The creation of one object inside of another is known as composition. With composition, the child's lifecycle is managed by the parent. Aggregation is when the lifecycle of the parent and child are separate.

<!-- conc -->

##### New For Requests

###### Passing Arguments to a Service

###### Refactoring to a Command

#### Using "new" in Application Services

#### Exceptions for Domain Events

#### Pushing Creation Logic into Factories

- Creation logic is boring, dull detail.

#### Using Static Factory Methods

### Static Cling

---

#### Timestamps

#### Loggers

### Lesson Learned

---

#### Creating Too Many Interfaces

### Concrete Dependencies

---

#### Concrete Dependencies in Code

#### Keeping Dependencies Abstract

### Coding Horror Story

---

#### Not So Groovy GroovyScript

### Inheritance

---

#### Too Many Inheritance Levels

#### Multiple Inheritance

#### Open-Closed Principle

<!-- Less Logic in base classes the better -->
<!-- No concrete base classes, prefer interfaces or abstract -->

- Employee class that calculates pay
- Calculate pay for salaried and non-salaried employees

#### Liskov Substitution

<!-- Base class makes assumptions -->
<!-- No concrete base classes, prefer interfaces or abstract -->

- Tomatoes and zucchini are berries, broccoli is a flower
- Is a hot dog a sandwich?
- LSK is a rule for poorly defined interfaces

### Career Advise

---

#### Keeping Your Employer Happy

### Friend and Internal Classes

---

#### C++ Friendships

#### The "Internal" Keyword

### Engineering Disasters

---

#### MIM-104 Patriot Missile Launcher

**Image 8-X** A Patriot SAM system

![Patriot SAM System](../images/chapter-8/patriot-system.jpg)

On February 25, 1991, a SCUD missile launched by the Iraqi forces during the Gulf War hit a US Army barracks, killing 24 soldiers. Sitting in the way of the Iraqi SCUD was supposed to be the MIM-104 "Patriot" missile launcher system. The MIM-104 was a mobile SAM (surface-to-air-missile) that used a complex set of radar guided missiles to intercept and destroy incoming projectiles launched by enemy forces.

While Patriot had been very successful so far in the conflict, this failure was unfortunate because the lives of these twenty-four soldiers was due to a software bug. Patriot's computers at the time had been running for over 100 hours when the SCUD was initially detected. At this time the internal computer that handles timestamps was inaccurate to about one-third of a second. Translating to an operating error of about 600 meters due to the rate of speed at which the SCUD travels. The computer which handles predicting where the missile will travel to next was not able to calculate where the SCUD's exact placement was. As a result of this disastrous error, the intercept attempt was never made, and twenty-four soldiers lost their lives.

The lesson from Patriot is that software needs to be resistant to long running expose.
<!-- expand, needs source reference -->

### Dependency Inversion

---

Building on what we discussed in chapter four, dependency inversion is one technique used to build loosely coupled applications. We can design our application services to declare what interfaces they require, and our DI container will inject the necessary dependencies when they are needed. This will allow us to avoid writing complicated factories that may have to create multiple layers of dependencies.

#### How To Approach DI For Your Application

---

##### Dealing With Concrete Only Classes

One of the more common issue with C# is that the DateTime class does not have an interface. There are plenty of situations where you may need to utilizing the DateTime class in your application.

**Figure 8-X** Class that needs to compare a timestamp.

```csharp
    public class ServiceThatUsesDateTime : ICustomerService
    {
        public void Handle(ChangeCustomerReservationTime request)
        {
            if (request.DateTime < DateTime.Now)
            {
                throw new ArgumentOutOfRangeException();
            }
        }
    }
```

In the code above, we see that our application service looks to see if the timestamp from the request can be updated. If the timestamp is in the past, we throw an exception. The problem is, how to we unit test this? The Now method in the DateTime class is static. Sure you could pass a DateTime.Max and it would never be smaller than the DateTime.Now; but that would be bad coding. When you use static methods that are out of your control you are introducing undefined behavior into your code. Undefined behavior in code leads to inconsistent results and tests that will fail or succeed randomly. We want tests that will always fail or succeed give a certain condition. Our answer is to introduce an interface that can mimic the exact same methods in the DateTime class.

**Figure 8-X** ISystemClock interface.

```csharp
    public interface ISystemClock
    {
        DateTime Now();
    }
```

We have created an interface that has the same method signature as the Now method. All we need to do is wrap the DateTime class in an implementation class.

**Figure 8-X** SystemClock implementation.

```csharp
    public class SystemClock : ISystemClock
    {
        public DateTime Now()
        {
            return DateTime.Now;
        }
    }
```

If our SystemClock class, we implement the interface and call the static DateTime methods. We can now update our application service by injection our new ISystemClock interface into the constructor and calling the method.

**Figure 8-X** Updated application service that accepts an ISystemClock interface.

```csharp
    public class ServiceUsingISystemClock : ICustomerService
    {
        private readonly ISystemClock _clock;

        public ServiceUsingISystemClock(ISystemClock clock)
        {
            _clock = clock;
        }

        public void Handle(ChangeCustomerReservationTime request)
        {
            if (request.DateTime < _clock.Now())
            {
                throw new ArgumentOutOfRangeException();
            }
        }
    }
```

Our application service now correctly accepts an interface. This means we can easily mock said interface for our unit test.

**Figure 8-X** Unit test for our application service.

```csharp
    [TestClass]
    public class ServiceUsingISystemClockTests
    {
        private readonly ICustomerService _service;

        public ServiceUsingISystemClockTests()
        {
            var clock = new Mock<ISystemClock>();
            clock.Setup(x => x.Now()).Returns(new DateTime(2000, 1, 1));

            _service = new ServiceUsingISystemClock(clock.Object);
        }

        [TestMethod]
        public void Handle_ShouldThrowException()
        {
            var request = new ChangeCustomerReservationTime
            {
                DateTime = new DateTime(1999, 1, 1),
            };

            Assert.ThrowsException<ArgumentOutOfRangeException>(() => _service.Handle(request));
        }
    }   
```

> Info :large_blue_circle:
>
> One reason for lessening your reliance on static methods that is they can't be mocked. The same goes for extension methods, which are by definition static as well. Every static method in your application should be "Pure". That is, it has no side-effects and produces to the same output every time you give it a certain input.

Now that you know how to create a fake interface for the DateTime class. The same rules apply to other commonly classes that may require their own customer interface. The "Random" class for generating numbers, and "HttpClient" may need their own interface if you are using them in your solution. Follow the same method of creating an interface that contains methods with the same signatures as the classes are are wrapping. Then create a concrete wrapper class that will call the same method in the class you are wrapping.

> Info :large_blue_circle:
>
> Every mocking framework is just an implementation of the Proxy pattern. And the proxy pattern is simply just one way of implementing polymorphism. That is why you can't mock concrete classes, because you need an interface to begin with.

In all of the examples so far, we have looked at the proper way for implementing dependency injection. I want to show you some situations that are incorrect. You will notice in that all of these situations, the ability to unit our code is either non-existent, or greatly hampered. Well engineered code is always easy to test.

#### Resolving From A Static Factory

---

Lets look at what happens if we decide to forgo the use of dependency inversion. Below we have a standard factory that will create our application service by creating everything manually. We access the service by calling the factory method inside of a controller action.

**Figure 8-X** Raw factory creation.

```csharp
    public static class HandlerFactory
    {
        public static ICustomerService CustomerService()
        {
            return new CustomerService(new CustomerRepository(new CustomerContext()), new CustomerFactory());
        }

        // Lots more dependencies to wire up. Gets annoying very fast.
    }
```

When we write our own factories, we have to "new" everything ourselves. Classes which share the same dependencies mean that we have to repeat ourselves. Writing factories turns into a tedious activity quickly.

**Figure 8-X** Using our raw factory inside of a controller action.

```csharp
    [ApiController]
    [Route("api/[controller]")]
    public class BadCustomerController : ControllerBase
    {
        [HttpPost]
        public IActionResult ChangeCustomerReservationTime(ChangeCustomerReservationTime request)
        {
            if (!ModelState.IsValid)
            {
                return BadRequest();
            }

            var handler = HandlerFactory.CustomerService();

            handler.Handle(request);

            return Accepted();
        }
    }
```

In the example above, our code is coupled to the hard implementation of our service. We are unable to mock the implementation of our service because the creation of the class is contained within the method. This means the only testing we will be able to do in an integration test. Because integration tests are slower to run and harder to maintain, we are making our life difficult by not enabling proper unit testing.

Creating our dependencies manually is not a good idea because of all the drawbacks that come with:

- Writing code that we don't have to

- Having to repeat ourselves many, many times

- Only having access to a single lifetime, unless we want write our own singleton base class (Which we should not do)

- Many modern web frameworks today just assume that you are going to dependency injection in the first place

- Unit testing is impossible in the previous example because we are locked to a certain implementation

#### Utilizing A Dependency Injection container

---

Instead of manually creating our dependency chains, we are now using a DI container to register our dependencies. I am using the default Microsoft container that is standard for all ASP.NET Core web applications. You may choose any container if you wish, however I suggest starting out with the default container because of its simplicity and ease of use.

**Figure 8-X** After using a DI container.

```csharp
    public class CustomerModule
    {
        public void Register(IServiceCollection collection)
        {
            collection.AddDbContext<CustomerContext>();
            collection.AddTransient<CustomerFactory>();
            collection.AddTransient<ICustomerRepository, CustomerRepository>();
            collection.AddTransient<ICustomerService, CustomerService>();
        }
    }
```

While it may seem this is more work to register our dependencies this way, your effort will pay off in the long run. Anytime you need any of these dependencies for a future class or handler, they will already be registered for you. You will see later on how we can write a few methods that allow us to automatically register certain classes. This will further cut down on the amount of boiler plate code required to register our DI container.

Beware of your dependency chains when you are registering classes. Containers make it very easy to register dependencies, but this comes at a cost of potentially creating very long and complex dependency chains. In a very short time, you may have a tree that is four, five, or even six plus level deep. This becomes very apparent when you are trying to test a class that has a long list of dependencies to inject. If you go over four levels of dependencies, look to see if you can refactor to flatten out things.

Now that we are relying on our DI container to resolve our dependencies, we can update our controller class accordingly.

**Figure 8-X** Allowing the container to resolve dependencies for us.

```csharp
    [ApiController]
    [Route("api/[controller]")]
    public class GoodCustomerController : ControllerBase
    {
        private readonly ICustomerService _customerService;

        public GoodCustomerController(ICustomerService customerService)
        {
            _customerService = customerService;
        }

        [HttpPost]
        public IActionResult ChangeCustomerReservationTime(ChangeCustomerReservationTime request)
        {
            if (!ModelState.IsValid)
            {
                return BadRequest();
            }

            _customerService.Handle(request);

            return Accepted();
        }
    }
```

Instead of calling a factory method, we are declaring a dependency on our customer service interface, and having it passed to us through the constructor. By doing so, we are not tied to a concrete implementation of the service and can now mock the class properly in a unit test.

**Figure 8-X** Unit test setup for our customer service.

```csharp
    [TestClass]
    public class CustomerControllerTest
    {
        private readonly GoodCustomerController _controller;

        public CustomerControllerTest()
        {
            var customerService = new Mock<ICustomerService>();

            customerService.Setup(x => x.Handle(It.IsAny<ChangeCustomerReservationTime>()));

            _controller = new GoodCustomerController(customerService.Object);
        }

        [TestMethod]
        public void ChangeCustomerReservationTime_InvalidModel_ReturnsBadRequest()
        {
            // Implement unit test
        }
    }
```

Our unit test can now take advantage of constructor injection by allowing us to mock our customer service. Before, this type of unit test was impossible due to the static nature of how the service was initialized. With constructor injection, we open up the class to passing a mock, giving us the opportunity to write a quality unit test.

#### Other Dependency Injection Mishaps

The following DI methods all share the same fundamental flaw of inhibiting unit testing, or making it much harder than it should be.

##### Relying On Dispose

Using dispose instead of property dependency injection is not an acceptable alternative. Dispose is for clearing resources that *we* control. Dependency Injection revolves around declaring what interface is implemented by what class, and then letting the container do all the hard lifting.

**Figure 8-X** Relying on dispose instead of dependency injection.

```csharp
    public class DisposingFromTheContainer : ICustomerService
    {
        public void Handle(ChangeCustomerReservationTime request)
        {
            using (var repository = new CustomerRepository(new CustomerContext()))
            {
                // Do something with your repository.
            }
        }
```

Another issue with the code above is that you would have to implement the "IDisposable" interface on every class. You would just be adding more code than was is necessary. This code is also not unit testable due to being hard coupled to the concrete implementation of the CustomerRepository.

While Dispose is a good pattern when dealing with files and socket connections, it is not an appropriate means for managing the lifetime of dependencies.

##### Injecting The Container

Injecting the container itself is strongly advised against for a number of reasons. The first of which is you are leaking details related to the infrastructure part of your code into the application. The application should know nothing about the infrastructure of your solution. This includes what you are using to persist your data, how you are logging, and what DI container you are using. Secondly, you would still have to resolve from the container to get the dependency you want, this goes against the Hollywood Principle, of "don't call us, we'll call you".

---

Incorrect :x:

**Figure 8-X** Injecting the container directly.

```csharp
    public class InjectingTheContainer : ICustomerService
    {
        private readonly IServiceProvider _serviceProvider;

        public InjectingTheContainer(IServiceProvider serviceProvider)
        {
            _serviceProvider = serviceProvider;
        }

        public void Handle(ChangeCustomerReservationTime request)
        {
            // Handle the request.
        }
    }
```

While on the surface this may seem ok because; you are using constructor injection, and you are injecting and interface. This is still inappropriate. You would be adding an additional step where you would still need to resolve the dependency need to complete the transaction. And while yes, you could technically unit test this by giving the class a different version of the container. You would be going through a lot of effort to do something that can already be taken care for you.

---

##### Resolving From The Container

Before I go over this scenario I was to clear up a misconception that may come up. I fully realize that the situation I am showing below was used in an example before when we talked about our Mediator example. The difference between the previous example and the example below is that the previous example was used in library code, where this is application level code. There are subtle and unique differences between writing a library or third-party code, and writing an application. This is one of them where something may be appropriate in one situation, and not appropriate in another.

Resolving from the container is the natural follow up to trying to inject the container from the previous example. In the code below, we inject the container then attempt to resolve the ICustomerRepository directly from the container.

---

Incorrect :x:

**Figure 8-X** Resolving From The Container

```csharp
    public class ResolvingFromTheContainer : ICustomerService
    {
        private readonly IServiceProvider _serviceProvider;

        public ResolvingFromTheContainer(IServiceProvider serviceProvider)
        {
            _serviceProvider = serviceProvider;
        }

        public void Handle(ChangeCustomerReservationTime request)
        {
            var repository = _serviceProvider.GetService<ICustomerRepository>();

            // Handle the request.
        }
    }
```

Similar to the previous example, injecting the container  and resolving from it is doing additional work that the container is already written to solve for you. When you declare a dependency in your constructor, the container will automatically call the GetService for you and pass it into the constructor. So again, while you *could* unit test this; all it would mean is extra work and code that would be costly to maintain in the long run.

---

##### Over Injecting

One common mishap that is prevalent in older code bases is many dependencies injected into a single class. Typically this classes started with one or two dependencies, then over time grew to having more and more as new features were added on. The issue for this comes down to unit tests start to get very large and unwieldy in nature. You have to mock a lot of interfaces. Some of which are not used at times, breaking Interface Segregation in the process.

---
Incorrect :x:

**Figure 8-X** Injecting too many dependencies into a class.

```csharp
    public class OverInjecting : ICustomerService
    {
        private readonly IEventBus _eventBus;
        private readonly ISystemClock _systemClock;
        private readonly IReservationService _reservationService;
        private readonly ICustomerRepository _customerRepository;
        private readonly CustomerFactory _customerFactory;

        public OverInjecting(IEventBus eventBus, ISystemClock systemClock, IReservationService reservationService, ICustomerRepository customerRepository, CustomerFactory customerFactory)
        {
            _eventBus = eventBus;
            _systemClock = systemClock;
            _reservationService = reservationService;
            _customerRepository = customerRepository;
            _customerFactory = customerFactory;
        }

        public void Handle(ChangeCustomerReservationTime request)
        {
            // Handle the request.
        }
    }
```

In the unit test below, you can see how we have to mock a large number of interfaces.

**Figure 8-X** Mocking multiple interfaces in our class.

```csharp
    [TestClass]
    public class OverInjectingUnitTests
    {
        private readonly ICustomerService _customerService;

        public OverInjectingUnitTests()
        {
            var eventBus = new Mock<IEventBus>();
            // Setup eventBus mock.

            var systemClock = new Mock<ISystemClock>();
            // Setup systemClock mock.

            var reservationService = new Mock<IReservationService>();
            // Setup reservationService mock.

            var customerRepository = new Mock<ICustomerRepository>();
            // Setup customerRepository mock.

            _customerService = new OverInjecting(eventBus.Object, systemClock.Object, reservationService.Object, customerRepository.Object, new CustomerFactory());
        }

        [TestMethod]
        public void Handle_DoesSomethingWeExpectToHappen()
        {
            // Unit testing code.
        }
    }
```

In the unit test above I left out setting up the mocks for clarity sake. As you can see, there is a lot of code in this unit test already, and more is still needed. Our tests should be just as clean and concise as our executing code is. When you are injecting a large number of dependencies into a class, you are taking on the burden of large and complicated tests that may become cumbersome and hard to maintain in the future. By keeping the dependencies in your class to the lowest number possible, you allow for easy testing.

---

---
Food For Thought :apple:

There is no "hard" number of dependencies that a class should have. My personal maximum is four. If you go over that, look for ways to refactor your class. This is typically a good area where a Chain of Responsibility may come in handy.

---

##### Inappropriate Registration

I am not calling the next section something to avoid because there are always exceptions to certain rules. However, I would strongly advise against "getting fancy" with registering your dependencies that some containers provide the capability for. Constructor injection should solve 99% of your DI issues. If you feel as if you need to do any of the following, I would strongly advise you to re-think the issue at hand and see if there is an alternative way to what you perceive to be the correct solution.

---
Careful :eyes:

- Property injection
- Method injection
- Child containers
- Injecting value types
- Extravagant conditional registration
- Dynamic registration
- Writing any library to extend the container
- Custom lifetimes

---

##### Dependency Swallowing

Something to remember when you register you dependencies is the lifecycle of each dependency and how it relates to other dependencies it may rely on.

Look at following figure below, here we have a class called "RegisteredAsSingleton" which takes a dependency on "RegisteredAsTransient".

<!--  UML Diagram for class relation -->

---
Incorrect :x:

**Figure 8-X** Singleton class with Transient dependency.

```csharp
    public class RegisteredAsSingleton
    {
        private readonly RegisteredAsTransient _transient;

        public RegisteredAsSingleton(RegisteredAsTransient transient)
        {
            _transient = transient;
        }
    }
```

The issue pertains to how each class's lifecycle is registered. Because "RegisteredAsSingleton" will never be disposed on. It will hold onto "RegisteredAsTransient" for its entire life as well. This would effectively turn the transient class into a singleton as well. The same issue would occur for a singleton to hold a scoped dependency, or a scoped dependency to hold a transient one.

The table belows shows what kind of lifecycle a dependency can hold.

| Class Lifetime | Should Only Hold |
| -------------- | ---------------- |
| Singleton      | Singleton or nothing |
| Scoped         | Scoped, Singleton or nothing |
| Transient      | Anything |

---

##### Relying on a Service Locator instead of Dependency Injection

#### Dependency Injection Suggestions

- If you are using ASP.NET Core, I suggest going with the built in Microsoft container. It does not support features such as property injection, custom lifetimes, or child containers. This is a good thing. I doubt you will every need any of these features. 99% of what you need in a DI container is just standard constructor injection.

- Do not leak the details of your DI container into your domain or application. Those parts of your code should have no knowledge a DI container exists in the first place.

- If your solution starts to get large, you can use the Module pattern to break up the registration into smaller parts.

#### What should I register my dependencies as?

| Dependency | Lifetime |
| ---------- | -------- |
| Factories    | Transient or Instance per Request |
| Repositories | Transient or Instance per Request |
| Application Services | Transient or Instance per Request |
| Domain Services | Transient or Instance per Request |
| EF DBContext | Scoped or Instance per Lifetime Scoped |
| HTTP Clients | Singleton or Instance per Lifetime |
| gPRC/SOAP Channel Factories | Singleton or Instance per Lifetime |

If you are unsure, the question you want to ask yourself is what is the lifecycle of the dependency?

- Should it have a new instance every time I ask for it? - Transient

- Is its' lifecycle dependent on the request? - Scoped

- Should it never be disposed of? - Singleton

---
Info :memo:

If you want to cut down on registering some of your dependencies, you can utilize reflection to automatically register classes that match a particular pattern. The first bit code below takes every class that ends with "Factory" and will register them as transient. The second bit finds all concrete classes that end with "Repository" and registers them alongside the interface they implement. I only suggest doing this if you and your team are comfortable with reflection and understand what you are doing.

```csharp
    public void Register(IServiceCollection collection)
    {
        var assembly = Assembly.GetExecutingAssembly();

        assembly
            .GetTypes().Where(type => type.Name.EndsWith("Factory"))
            .ToList().ForEach(type => collection.AddTransient(type));

        assembly
            .GetTypes().Where(type => type.Name.EndsWith("Repository") && !type.IsAbstract && !type.IsInterface)
            .ToList().ForEach(type => collection.AddTransient(type.GetInterfaces().First(), type));
    }        
```

---

### Food for Thought

#### SRP vs DRY

### Conclusion
