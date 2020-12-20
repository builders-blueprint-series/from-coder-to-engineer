If you can only do one thing well in your application, make sure it is encapsulated. A lack of encapsulation will slow down and break an application faster than anything else.

To help explain how proper encapsulation will better your application, we will continue with the reservation system example we introduced in the previous chapter.

**Listing 11-1** Our Customer class with public setters.

```csharp
    public class Customer
    {
        public Guid Id { get; set; }

        public string FirstName { get; set; }

        public string LastName { get; set; }

        public string Email { get; set; }

        public int LoyaltyPoints { get; set; }

        public int PointsYearToDate { get; set; }
    }
```

Our Customer class contains a handful of properties. For simplicity, we have keep all of these properties to basic types.

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

# Chapter 11

## Encapsulation

After completing this chapter, you will be able to

- Understand how public setters break encapsulation

- Push private functions down into other objects

- Understand where and when to use extension methods

- See how exposed collections break encapsulation

- Understand why domain logic shouldn't escape the application boundary

- Learn how to enforce encapsulation with Entity Framework

- Weigh the limits of encapsulation in certain situations

### At a glance

<!-- Find a way to emphasize crafting over foundation -->
1. Encapsulation as a foundation for good coding techniques

2. Encapsulation issues compound together

3. Properly encapsulated code results in better testing

4. Properly encapsulated code limits options, shortens the feedback loop, and enforces simplicity through a common interface

#### Public Setters

1. Problem Statement

    - Listing Customer with public setters

        1. Code overview

    - Listing Service that uses Customer

        1. Code overview

    - Listing Tests for service

        1. Code overview

2. Issues at hand

    - Client access to details

        1. Naked details

            - Customer details are open clients

                1. Code details

            - Example of SSN

        2. Objects mimic real life interactions

            - Objects are interfaces
            - Objects that limit details shorten the feedback loop

    - Invariants can not be protected

        1. Scattered Validation

            - Listing Customer validation in service

                1. Code details

            - Listing Customer validation in repository

                1. Code details

            - Validation statements cause extra testing

        2. State can not be guaranteed

            - Listing State in first Customer

                1. Code details

            - Listing State in second Customer

                1. Code details

            - Having to check state cause extra testing

        3. Objects are masters of their information

            - Objects should self-validate
            - Objects should be aware their state at all times
            - Fewer state options translates to fewer tests and bugs

    - Non-centralized creation

        1. Object changes cascade throughout application

            - Note: What is Shotgun Surgery
            - Listing Customer has property added to it

                1. Code details

            - Customer creation must be updated in entire code base

        2. Objects should limit their creation options

            - Centralized object creation produces a common interface

3. Converting to private setters

    - Listing Customer now has private setters

        1. Code details

4. Embracing Constructors

    - Listing Customer now has constructor

        1. Code details

    - Constructors are common interfaces

5. Clarity through static methods

    - Listing Customer created via new acquisition

        1. Code details

    - Listing Customer created via status match

        1. Code details

    - Static methods provide intent and clarity

6. Improved testing

    - Testing has become easier to understand and maintain

7. Conclusion

    - Public setters create an endless amount of possibilities for object creation and state, resulting in extraneous code
    - Private setters with constructors limit these possibilities, resulting in less code, fewer bugs, and a simplified code base

#### Private Methods

1. Show application service with private methods

2. Show why private method harm a code base

3. Private methods can't be tested

4. Private methods indicate poorly placed code

5. Show how to push private methods down

6. Code can now be tested properly

7. Section conclusion

#### Extension Methods

1. Some things require extension methods

2. Show code with improper extension methods

3. Extension methods are hard to mock

4. Extension methods have to be static

5. Extension methods are bound to all types

6. Show how to move extension methods into classes

7. Code can now be tested properly

8. Section conclusion

#### Exposed Collections

1. Show class that returns an exposed collection, where logic is
performed on collection afterwards

2. Show why exposed collections make testing harder

3. Exposed collections can be changed at any time

4. Exposed collections don't convey meaning

5. Show how to move collections into dedicated type

6. Code can now be tested properly

7. Section conclusion

#### Leaky Domain Logic

1. Show domain classes that have leaked to the presentation

2. Explain why clients don't care about your domain

3. Show why changing the domain forces clients to adapt

4. Show how having specified responses for the same domain allow for simplified code

5. Show how to move domain objects into specific responses

6. Code is now easier to change

7. Section conclusion

#### Encapsulation with Entity Framework

1. Entity Framework means tradeoffs with encapsulation

2. Basic encapsulation with Entity Framework

3. EFCore allows for primitives to be fully encapsulated

4. Creating Value types in EFCore

5. Complex types require certain compromises

6. Encapsulating collections in EFCore

7. Section conclusion

#### Limits of Encapsulation

It is important to understand that achieving full encapsulation will probably not occur. There are a number of commonly used libraries and frameworks that do not allow for full encapsulation. Entity Framework for complex types requires private setters and a private constructor. Swagger, the common library for implementing the Open API standard requires public setters. Model Binding and other JSON deserializers also require private setters and an empty constructor.

Sometimes we are required to make concessions when and when we can apply encapsulation. As an engineer it is up to you to weigh the advantages and disadvantages of every decision you make.