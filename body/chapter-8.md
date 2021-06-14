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

**Figure 8-X** Raw factory creation.

```csharp
    public static class HandlerFactory
    {
        public static IHandleRequest<ChangeCustomerReservationTime> ChangeCustomerReservationTimeHandler()
        {
            return new ChangeCustomerReservationTimeHandler(new CustomerRepository(new CustomerContext()), new CustomerFactory());
        }

        // Lots more dependencies to wire up. Gets annoying very fast.
    }
```

**Figure 8-X** Using our raw factory.

```csharp

```

Creating our dependencies manually is not a good idea because of all the drawbacks that come with:

- Writing code that we don't have to

- Having to repeat ourselves many many times

- Only having access to a single lifetime, unless we want write our own singleton base class. (Which we should not do.)

- Many modern frameworks today just assume that you are going to dependency injection in the first place

**Figure 8-X** After using a DI container.

```csharp
    public class CustomerModule
    {
        public void Register(IServiceCollection collection)
        {
            collection.AddDbContext<CustomerContext>();
            collection.AddTransient<CustomerFactory>();
            collection.AddTransient<ICustomerRepository, CustomerRepository>();
            collection.AddTransient<IHandleRequest<ChangeCustomerReservationTime>, ChangeCustomerReservationTimeHandler>();
        }
    }
```

**Figure 8-X** Allowing the container to resolve dependencies for us.

```csharp

```

While it may seem this is more work to register our dependencies this way, your effort will pay off in the long run. Anytime you need any of these dependencies for a future class or handler, they will already be registered for you. You will see later on how we can write a few methods that allow us to automatically register certain classes. This will further cut down on the amount of boiler plate code required to register our DI container.

### Dependency Injection Best Practices

- If you are using ASP.NET Core, I suggest going with the built in Microsoft container. It does not support features such as property injection, custom lifetimes, or child containers. And you probably will never need any of these features anyway.

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
