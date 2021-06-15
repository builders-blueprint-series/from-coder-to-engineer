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

Our unit test can now take advantage of constructor injection by allowing us to mock our customer service.

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
