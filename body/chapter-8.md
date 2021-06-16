# Chapter 8

## Crush Coupling

**After completing this chapter, you will be able to**

- Understand how coupling hurts application development

- Understand how coupling inhibits testing

- Learn how to reduce coupling by avoid certain language features

- Know when and where to apply inheritance

- Implement dependency inversion the correct way

<!-- Introduction -->

### Dependency Inversion

Building on what we discussed in chapter four, dependency inversion is one technique used to build loosely coupled applications. We can design our application services to declare what interfaces they require, and our DI container will inject the necessary dependencies when they are needed. This will allow us to avoid writing complicated factories that may have to create multiple layers of dependencies.

#### Resolving From A Static Factory

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

---
**Note** :memo:

Beware of your dependency chains when you are registering classes. Containers make it very easy to register dependencies, but this comes at a cost of potentially creating very long and complex dependency chains. In a very short time, you may have a tree that is four, five, or even six plus level deep. This becomes very apparent when you are trying to test a class that has a long list of dependencies to inject. If you go over four levels of dependencies, look to see if you can refactor to flatten out things.

---

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

##### Relying On Dispose

Using dispose instead of property dependency injection is not an acceptable alternative. Dispose is for clearing resources that *we* control. Dependency Injection revolves around declaring what interface is implemented by what class, and then letting the container do all the hard lifting.

---
Incorrect :x:

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

---

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

---
Note :memo:

Before I go over this scenario I was to clear up a misconception that may come up. I fully realize that the situation I am showing below was used in an example before when we talked about our Mediator example. The difference between the previous example and the example below is that the previous example was used in library code, where this is application level code. There are subtle and unique differences between writing a library or third-party code, and writing an application. This is one of them where something may be appropriate in one situation, and not appropriate in another.

---

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

### Dependency Injection Suggestions

- If you are using ASP.NET Core, I suggest going with the built in Microsoft container. It does not support features such as property injection, custom lifetimes, or child containers. This is a good thing. I doubt you will every need any of these features. 99% of what you need in a DI container is just standard constructor injection.

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
