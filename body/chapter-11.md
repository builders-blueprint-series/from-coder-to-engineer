# Chapter 11

## Encapsulation

After completing this chapter, you will be able to

- Understand how public setters break encapsulation

- Push private functions down into other objects

- Understand where and when to use extension methods

- See how exposed collections break encapsulation

- Understand why domain logic shouldn't escape the application boundary

If you can only do one thing well in your application, make sure it is encapsulated. A lack of encapsulation will slow down and break an application faster than anything else.

To help explain how proper encapsulation will better your application, we will continue with the reservation system example we introduced in the previous chapter.

**Listing 11-1** An example of a class with public setters.

```csharp
    public class Customer
    {
        public Guid Id { get; set; }

        public string FirstName { get; set; }

        public string LastName { get; set; }

        public string Email { get; set; }

        public int LoyaltyPoints { get; set; }

        // Year To Date
        public double PointsYTD { get; set; }

        public LoyaltyStatus Status { get; set; }

        public ICollection<Reservation> Reservations { get; set; } = new List<Reservation>();
    }
```

Our Customer class contains a handful of properties, and a collection of Reservations which here signifies that we are using Entity Framework and Reservations represents a One-To-Many relationship where a single customer may have multiple reservations.

The issue with public setters is:

1. All data can be changed by any class that holds a reference

2. Invariants can't be protected

3. Creation of Customer is not centralized

There is nothing to stop a person from doing the following:

**Listing 11-2** Public setters allow for any change

```csharp
    // Retrieve customer from persistence

    Customer customer;

    customer.Id = Guid.NewGuid();
    customer.Status = LoyaltyStatus.Gold;

    // Save to persistence
```

This code changes the Id of the customer to a different GUID, and updates their status to Gold.

A common reaction to this code sample:

1. "No one would actually do this."

2. "My developers know better."

3. "We'll catch it in code review".

Remember, its not that this situation did occur, it's that it could occur. Private setters protect against this scenario from happening, because the complier does not allow you to do so.

**Listing 11-3** Invariants can't be protected

```csharp
    customer.Email = "IForgotTheDotCom@MyDomain";
```

Looking at this, we can assign any string value to our Email property, and the mistake will only be known to us when we try to send the email.

### Where Encapsulation is not possible

It is important to understand that achieving full encapsulation will probably not occur. There are a number of commonly used libraries and frameworks that do not allow for full encapsulation. Entity Framework for complex types requires private setters and a private constructor. Swagger, the common library for implementing the Open API standard requires public setters. Model Binding and other JSON deserializers also require private setters and an empty constructor.

Sometimes we are required to make concessions when and when we can apply encapsulation. As an engineer it is up to you to weigh the advantages and disadvantages of every decision you make.
