# Encapsulation

## Key Terms

Encapsulation

## Introduction

## Goals

After completing this chapter will you will be able to...

Understand how public setters breaks encapsulation.

Identity where methods belong and how access modifies affect the ability to test code.

Spot where naked collections and properties can be consolidated.

## Wisdom

> "You're on a need-to-know basis, and you don't need to know." (Simpson, Bruckheimer, & Bay, 1996).

## The Success of Southwest Airlines - Simplicity In the Air

In the highly competitive and complicated world of air transportation, Southwest airlines has made a name for itself by flying against the air current of convention. Their business model is the definition of simplicity in the airline world. Southwest does not operate hubs, instead they fly mostly point-to-point. This eliminates the need for many travelers to connect. Less connections means passengers do not have to worry about a checked bag not making it to the next plane-or a weather delay causing a missed connection. They only operate one type of plane, the 737. They can save money by only needing to train, perform maintenance, and have pilots qualified for a single aircraft. They only have one type of seating, all coach. Southwest does not sell food on board, and they do not assign seats. In addition, they provide a simple bundled fare system to their customers. Southwest customers know exactly what they are getting when they buy a ticket. A simple, straight forward, no-frills experience that gets them to their destination. Great software is like Southwest airlines. Your end-users should get a consistent experience every time without any undue frustration.

## Code

### The Problem with Public Setters

- Public setters are one of the most abused issues in software. But they are also one of the easiest to fix.

#### Introduce topic of restaurant reservation system

- This book will use the example of a fake reservation system for code samples. A reservation system allows us to cover a breadth of topics that have real life business applications.

**Figure 1-1** The first iteration of our Customer class.

```csharp
    public class Customer
    {
        public Guid Id { get; set; }

        public string FirstName { get; set; }

        public string LastName { get; set; }

        public string Email { get; set; }
    }
```

- The Customer class will initially represent customers who wish to dine in our restaurant.

#### Public setters allow for anything and everything

- Public setters mean that any client can assign any value they wish.

**Figure 1-2** Public setters allow for clients to do anything to the contents.

```csharp
    public void AnythingCanHappen()
    {
        var customer = new Customer
        {
            Id = Guid.Empty,
            FirstName = "Jimbo123",
            LastName = null,
            Email = "notValid@spam",
        };
    }
```

- Public setters allow clients to initialize properties with any value, valid or not.

#### Public setters do not support internal validation

- Validation outside of a class leaks details about its internals.

**Figure 1-3** Validation occurs in some outside method, not the class.

```csharp
    public void HaveToValidateOutsideCustomer(string first, string last, string email)
    {
        if (first != null && last != null && email != null)
        {
            var customer = new Customer
            {
                Id = Guid.NewGuid(),
                FirstName = first,
                LastName = last,
                Email = email,
            };
        }
        else
        {
            throw new ArgumentNullException();
        }
    }
```

---
:large_blue_circle: Encapsulation goes beyond just basic getters and setters. A class should be the sole source of truth for its logic. That includes validation and state changes.

---

- Classes should not rely on outside influences about how they should conduct their business.

#### Public setters expose a public api for private details

- You want to keep classes closed as much as possible.

**Figure 1-4** Any method has access to every class property.

```csharp
    public Customer ICanDoAnything(Customer customer)
    {
        customer.Id = Guid.NewGuid();
        customer.FirstName = "RazzleDazzle";

        return customer;
    }
```

- Anything that you permit to happen, will happen.

#### The Frustration from Testing Public Setters Properly

- The biggest issue with testing public setters is the number of possible permutations. The number of possible permutations is almost infinite.

**Figure 1-5** Attempting to figure out how many branches to test

```csharp
    [TestClass]
    public class TestingPublicSetters
    {
        [TestMethod]
        public void Customer_Is_In_Correct_State()
        {
            var customer = new Customer
            {
                FirstName = "UghOh,No1AlphaNumer1C",
                LastName = "Sp@CiAlCharactersNotGood",
                Email = "notValidEmail.com",

                // Forgot to assign Id
            };

            // A lot of Asserts down here.
        }
    }
```

- Encapsulation reduces the number of possibilities the state of our objects can be in. This translates into less undefined behavior and bugs in our software.

---
:warning: In C# and other dotnet languages, Guid is a value type. If you forget to assign a value, it will default to all zeros. You will never get a null reference unless you make it nullable.

---

#### Transforming Public Setters to Private

- Transitioning our class to using private setters will allow us to fix the issues of having an open property api and the lack of internal validation.

##### Step 1: Making all setters private

- The first step is to remove all public setters from our code. By doing so, this will force anyone to wants to create an instance of a Customer via the constructor.

**Figure 1-6** Our Customer class after applying basic encapsulation

```csharp
    public class EncapsulatedCustomer
    {
        public EncapsulatedCustomer(string firstName, string lastName, string email)
        {
            Id = Guid.NewGuid();
            FirstName = firstName;
            LastName = lastName;
            Email = email;
        }

        public Guid Id { get; }

        public string FirstName { get; }

        public string LastName { get; }

        public string Email { get; }
    }
```

---
:heavy_check_mark: Constructors are one of the many examples of common interfaces we will discuss in this book. Common interfaces enforce a consistent state in our code which constrains options and reduces bugs, leading to better software.

---

**Figure 1-7** Clients required to now use the Customer constructor

```csharp
    public class ClientsHaveToUseAConstructor
    {
        public void MakeANewCustomer(string firstName, string lastName, string email)
        {
            // Force clients to use a constructor.
            var customer = new EncapsulatedCustomer(firstName, lastName, email);

            // Will not compile.
            var badCustomer = new EncapsulatedCustomer
            {
                FirstName = firstName,
                LastName = lastName,
                Email = email,
                Id = Guid.NewGuid(),
            };
        }
    }
```

- The example above shows the before and after of moving the initialization of our object from naked properties to a constructor. Moving initialization has also allowed us to reduce the line count in our application.

---
:large_blue_circle: Every line in an application has a cost associated with it. A good engineer looks to reduce line count where possible, but not at the expense of readability.

---

##### Step 2: Moving validation into the Customer object

- Currently our Customer object is validated from the outside. Moving the validation into the Customer object itself will simplify our code and testing.

**Figure 1-8** Validation of Customer is now internalized

```csharp
    public class CustomerWithInternalValidation
    {
        public CustomerWithInternalValidation(string firstName, string lastName, string email)
        {
            if (firstName != null && lastName != null && email != null)
            {
                Id = Guid.NewGuid();
                FirstName = firstName;
                LastName = lastName;
                Email = email;
            }
            else
            {
                throw new ArgumentNullException();
            }
        }

        public Guid Id { get; }

        public string FirstName { get; }

        public string LastName { get; }

        public string Email { get; }
    }
```

- Moving our validation statement into the constructor has allowed us to encapsulate our validation of the object as well. Unfortunately this does present a series of issues when we attempt to test this new implementation.

**Figure 1-9** Unit testing our updated Customer validation logic

```csharp
    [TestClass]
    public class TestingInternalCustomerValidation
    {
        [TestMethod]
        public void Customer_OnInitialization_HasCorrectProperties()
        {
            const string firstName = "Billy";
            const string lastName = "Bob";
            const string email = "Billy@Bob.com";

            var customer = new CustomerWithInternalValidation(firstName, lastName, email);

            Assert.AreEqual(firstName, customer.FirstName);
            Assert.AreEqual(lastName, customer.LastName);
            Assert.AreEqual(email, customer.Email);
        }

        [TestMethod]
        [DataRow("Billy", "Bob", null)]
        [DataRow("Billy", null, "Billy@Bob.com")]
        [DataRow(null, "Bob", "Billy@Bob.com")]
        public void Customer_OnNullInitialization_ThrowsException(string firstName, string lastName, string email)
        {
            Assert.ThrowsException<ArgumentNullException>(() => new CustomerWithInternalValidation(firstName, lastName, email));
        }
    }
```

---
:large_blue_circle: When testing multiple scenarios that only differ via their arguments like our test above, you can utilize the DataRow attribute to avoid having to write the same test boiler plate over and over again.

---

- In our tests we are ensuring that our constructor does not throw and exception when it is supplied with valid arguments. Our second test confirms that any null value will result in an exception being thrown.

---
:x: Do not use the DataRow attribute to hide bad programming. Excessive use of the attribute is mostly likely a sign of too many logical branches in your code.

---

- What happens if we have to add more properties to our class? Or test more than one scenario with our constructor? Current codes does not look for empty strings. Our validation does not work in other classes as well, we want easy reuse with our code.

**Figure 1-10** Our Customer validating more specific scenarios

```csharp
    public TooMuchCustomerValidationLogic(string firstName, string lastName, string email)
    {
        if (firstName != null
                && firstName != string.Empty
                && lastName != null
                && lastName != string.Empty
                && email != null
                && email != string.Empty
                && Regex.IsMatch(firstName, @"^[a-zA-Z]+$")
                && Regex.IsMatch(lastName, @"^[a-zA-Z]+$")
                && Regex.IsMatch(email, @"\b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}\b"))
        {
            Id = Guid.NewGuid();
            FirstName = firstName;
            LastName = lastName;
            Email = email;
        }
        else
        {
            throw new ArgumentNullException();
        }
    }

    public Guid Id { get; }

    public string FirstName { get; }

    public string LastName { get; }

    public string Email { get; }
}
```

- This will require a lot of unit testing to make sure all the possible branches are covered. Our one class is going to need dozens of tests to make this work. We need to allow our validation to move between classes and make it easy to read.

##### Step 3: Encapsulating our Validation logic

- Much like how our constructor encapsulated the creation of our Customer object, we need to encapsulate the details of our validation logic. Our Customer object should not be privy to the finite details of what makes an email valid, it just wants to know if the parameter is valid or not.

**Figure 1-11** Validation moved to a series of static methods

```csharp
    public static class Validator
    {
        public static void StringIsNotEmptyOrNull(string value)
        {
            if (string.IsNullOrEmpty(value))
            {
                throw new ArgumentNullException(nameof(value));
            }
        }

        public static void ValidNameFormat(string name)
        {
            if (!Regex.IsMatch(name, @"^[a-zA-Z]+$"))
            {
                throw new ArgumentException("Name is not match the required pattern.", nameof(name));
            }
        }

        public static void ValidEmail(string email)
        {
            if (!Regex.IsMatch(email, @"\b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}\b"))
            {
                throw new ArgumentException("Email is not in a valid format.", nameof(email));
            }
        }
    }
```

**Figure 1-12** Updated tests for our new Validation class

```csharp
    [TestClass]
    public class ValidationTests
    {
        [TestMethod]
        public void StringIsNotEmptyOrNull_ValidObject_DoesNotThrowException()
        {
            Validator.StringIsNotEmptyOrNull("Valid");
        }

        [TestMethod]
        public void StringIsNotEmptyOrNull_InvalidObject_ThrowsException()
        {
            Assert.ThrowsException<ArgumentNullException>(() => Validator.StringIsNotEmptyOrNull(string.Empty));
        }

        [TestMethod]
        public void ValidNameFormat_ValidName_DoesNotThrowException()
        {
            Validator.ValidNameFormat("Billy");
        }

        [TestMethod]
        public void ValidNameFormat_InvalidName_ThrowsException()
        {
            Assert.ThrowsException<ArgumentException>(() => Validator.ValidNameFormat("B1l)y"));
        }

        [TestMethod]
        public void ValidEmailFormat_ValidEmail_DoesNotThrowException()
        {
            Validator.ValidEmail("BILLY@BOB.COM");
        }

        [TestMethod]
        public void ValidEmailFormat_InvalidEmail_ThrowsException()
        {
            Assert.ThrowsException<ArgumentException>(() => Validator.ValidEmail("Billy.com@Bob"));
        }
    }
```

#### The Result of Zero Public Setters

#### The Easiness of Testing a Closed Classes

### The Problem with Private Methods

#### The Inability to Test Private Methods

#### Pushing Private Methods Down

##### Step N2

#### The Result of Moving Logic Down

#### The Ability to Test Easily Accessible Methods

### The Problem with Naked Properties and Collections

#### The Frustration with Testing Naked Properties and Collections

##### Step N3

#### The Result after Encapsulating Properties and Collections

## Hard Truth

Coding is easy. Engineering is hard.

Writing code and engineering software are two fundamentally different things. The end goal for both is the same. Both the engineer and the coder want a software solution that accomplishes the desired task in a reasonable amount of time. But the engineer and code get to the same conclusion through different methods. The engineer is retrospective as he works, drawing on past experiences, pausing to give thought, and contemplating choices. The coder, codes. He gets to a solution the fastest way possible. If the result works, then the ends have justified the means. The engineer understands that his work is a disciple. They must practice and refine their skills on a weekly basis. The coder looks at some quick documentation or sample code and attempts to piece together a solution. The engineer strives for simplicity everywhere. The coder chases the latest technology trends. The engineer understands that everything involves making a decision, acknowledging that trade-offs are required. The coder goes with their gut without contemplating advantages and disadvantages for each possible decision. Many people take the coding route. It is the path of least resistance.

Robert Frost's poem, "The Road Not Taken", express the decision at hand, "Two roads diverged in a wood, / and I—
I took the one less traveled by, / And that has made all the difference."

What road will you take?

## Vocation

Engineer is a mentality, not a title.

## Automation

Automate your decision making process.

Automation is not limited to just menial tasks that involve your application.

## Conclusion
