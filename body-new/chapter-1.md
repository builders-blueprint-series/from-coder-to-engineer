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

Great software echos the words of Dr. Stanley Goodspeed. Not everyone needs to know everything, including your fellow developers. Classes, abstractions, and architecture should be closed by default. Only give your clients the absolute minimum they need to complete their tasks. Providing more than what is necessary only invites trouble into your code base.

## The Success of Simplicity - How Southwest Stays Simple

Airlines are software companies. In the highly competitive and complicated world of air transportation, Southwest Airlines has made a name for itself by flying against the air current of convention. Their business model is the definition of simplicity in the airline world. Southwest does not operate hubs; instead, they fly mostly point-to-point. This eliminates the need for many travelers to make connecting flights. Fewer connections means passengers do not have to worry about a checked bag not making it to the next plane or a weather delay, causing a missed connection. They only operate one type of plane--the Boeing 737. They can save money by standardizing training, maintenance, and qualifications for pilots all with a single aircraft type. Seating is only coach. Southwest does not sell food on board, and they do not assign seats. In addition, they provide a simple bundled fare system to their customers, who know exactly what to expect. A simplistic, straight-forward, no-frills experience that gets them to their destination. Your end-users should get a consistent experience every time without any undue frustration. Great software is like Southwest Airlines.

## Code

### The Problem with Public Setters

- Public setters are one of the most abused issues in software. But they are also one of the easiest to fix.

#### Code Domain

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

- Public setters allow clients to initialize properties with any value--valid or not.

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

- Validation logic is now encapsulated in a set of methods. This allows us to reuse these methods in other classes that require the same kind of validation.

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

- With our validation logic encapsulated and tested, we can reuse these methods in any other domain classes that needed validation. We made it easier to test our application and promote code reuse in the same step.

#### The Result of Zero Public Setters

- The final result of our Customer object has its properties and validation fully encapsulated. It can only be instantiated via its' constructor which promotes a singular way of creating the object. All properties are now read-only which removes the ability of clients being able to change values at will.

**Figure 1-13** Final Customer object

```csharp
    public class FullyEncapsulatedCustomer
    {
        public FullyEncapsulatedCustomer(string firstName, string lastName, string email)
        {
            Validator.ValidNameFormat(firstName);
            Validator.ValidNameFormat(lastName);
            Validator.ValidEmail(email);

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

- If we need to add any more properties to this class, we can just use a validator we have already written, or write a new one without having to change or modify the existing class.

---
:large_blue_circle: If you are working in dotnet, FluentValidation is a great library that has many built in validators that is perfect for validating domain objects.

---

#### The Easiness of Testing a Closed Classes

- The best feature of our new class is that we've done all the testing in our validator tests. Because we are using auto-properties, there is nothing special to test.

---
:x: Do not test getters and setters in your code unless they perform logic. These are built in features of the language you are using and should not be tested.

---

### The Problem with Private Methods

- Private methods are problem areas in our application because they do not provide a public interface for testing. The issue with private methods is that they typically are performing logic in the wrong place. The main fix for these issues to to push the code down and provide a public API from where the code can be called and tested from.

#### Private methods do not expose a public API

- Let's assume that our Reservation Service needs to check if a reservation slot matches what a customer has provided between a range of DateTimes.

**Figure 1-14** Customer object updated with DateTime properties

```csharp
    public class Customer
    {
        // Other properties and methods omitted for brevity.

        public DateTime AvailableMinimum { get; }

        public DateTime AvailableMaximum { get; }
    }
```

- Customer object has two DateTime's that represent a range for for reservation availability.

- Our Customer object is used by our Reservation Service in two different methods. The first method takes care of when a reservation is cancelled and looks for customers that fit a specific reservation range. The second method encompasses a standard reservation booking.

**Figure 1-15** Reservation service that utilizes the Customer availability range

```csharp
    public class ReservationService
    {
        public void FindAvailableCustomers(List<Customer> customers, DateTime availability)
        {
            var fitAvailability = customers.Where(customer => AvailabilityMatchesCustomer(availability, customer.AvailableMinimum, customer.AvailableMaximum));

            // Take a customer and create reservation.
        }

        public void BookReservation(Customer customer, DateTime availability)
        {
            if (AvailabilityMatchesCustomer(availability, customer.AvailableMinimum, customer.AvailableMaximum))
            {
                // Book their reservation.
            }
        }

        private bool AvailabilityMatchesCustomer(DateTime reservationAvailability, DateTime min, DateTime max)
        {
            return reservationAvailability >= min && reservationAvailability <= max;
        }
    }
```

- Both the FindAvailableCustomers and BookReservation methods use the AvailabilityMatchesCustomers method to see if the time range the customer has specified matches the availability time slot.

#### The Inability to Test Private Methods

**Figure 1-16** Attempting to unit test the AvailabilityMatchesCustomer method

```csharp
    [TestClass]
    public class ReservationServiceTests
    {
        [TestMethod]
        public void AvailabilityMatchesCustomer_DateTimeMatchesRange_ReturnsTrue()
        {
            var service = new ReservationService();

            // Does not compile, inaccessible due to protection level.
            var result = service.AvailabilityMatchesCustomer(DateTime.UtcNow, DateTime.MinValue, DateTime.MaxValue);

            Assert.IsTrue(result);
        }
    }
```

- Our issue is stark-the compiler does not allow us to access private methods because the are by definition private. What we need is a public API to create unit tests from, but we do not want to change our method from private to public because we would be exposing a method that was not intended to be exposed.

---
:x: Do not test private methods by testing a public method that calls said private methods. This would be exposing the underlying details of the public method which breaks encapsulation.

---

#### Pushing Private Methods Down

- We have a small conundrum in our hands. We need to have a public method in order to write a test, but our method as it currently stands should not be public because it is not required by clients. Our solution for this issue is solved by moving our method from the ReservationService class to our Customer class. This create a number of positive side effects:

1) Solves our initial issue not not being able to test the method
2) Removes the need of our ReservationService from calling the min and max properties
3) Creates a more encapsulated Customer object by allowing us to remove the min and max getters

##### Step 1: Pushing logic down in the application

- The process of moving our logic is very simple and straight forward. We are going to move the method from the service to the Customer object and update the references.

**Figure 1-17** Customer object with new method

```csharp
    public class CustomerWithAvailabilityMethod
    {
        // Other properties and methods omitted for brevity.

        public DateTime AvailableMinimum { get; }

        public DateTime AvailableMaximum { get; }

        public bool AvailabilityMatchesCustomer(DateTime reservationAvailability, DateTime min, DateTime max)
        {
            return reservationAvailability >= min && reservationAvailability <= max;
        }
    }
```

- Our ReservationService can remove the method from its body.

**Figure 1-18** ReservationService sans our method

```csharp
    public class ReservationServiceWithOutMethod
    {
        public void FindAvailableCustomers(List<CustomerWithAvailabilityMethod> customers, DateTime availability)
        {
            var fitAvailability = customers.Where(customer => customer.AvailabilityMatchesCustomer(availability, customer.AvailableMinimum, customer.AvailableMaximum));

            // Take a customer and create reservation.
        }

        public void BookReservation(CustomerWithAvailabilityMethod customer, DateTime availability)
        {
            if (customer.AvailabilityMatchesCustomer(availability, customer.AvailableMinimum, customer.AvailableMaximum))
            {
                // Book their reservation.
            }
        }
    }
```

##### Step 2: Update the method to streamline the parameters

- Because our method is now defined inside of the Customer class, the ReservationService has no need to call the min and max property. We can update the method to just accept a single dateTime.

**Figure 1-19** Customer with update method parameters

```csharp
    public class CustomerWithUpdatedMethod
    {
        // Other properties and methods omitted for brevity.

        public DateTime AvailableMinimum { get; }

        public DateTime AvailableMaximum { get; }

        public bool AvailabilityMatchesCustomer(DateTime reservationAvailability)
        {
            return reservationAvailability >= AvailableMinimum && reservationAvailability <= AvailableMaximum;
        }
    }
```

**Figure 1-20** ReservationService with updated method calls

```csharp
    public class ReservationServiceWithUpdateMethodCalls
    {
        public void FindAvailableCustomers(List<CustomerWithUpdatedMethod> customers, DateTime availability)
        {
            var fitAvailability = customers.Where(customer => customer.AvailabilityMatchesCustomer(availability));

            // Take a customer and create reservation.
        }

        public void BookReservation(CustomerWithUpdatedMethod customer, DateTime availability)
        {
            if (customer.AvailabilityMatchesCustomer(availability))
            {
                // Book their reservation.
            }
        }
    }
```

- My having the AvailabilityMatchesCustomer method now use the Customer internal properties instead of accepting them as parameters we have simplified our method and how it is called in the ReservationService significantly.

---
:large_blue_circle: It may not be apparent, but reducing our parameter count has increased the encapsulation of our application. Our reservation is know unaware of what the parameter it is passing is being compared against.

---

##### Step 3: Converting the range properties to private

- Since our min and max properties now have zero references besides those from the Customer object itself, they no longer have a reason to remain public.

**Figure 1-21** Customer with private min and max values

```csharp
    public class CustomerWithPrivateValues
    {
        // Other properties and methods omitted for brevity.

        private readonly DateTime _availableMinimum;

        private readonly DateTime _availableMaximum;

        public bool AvailabilityMatchesCustomer(DateTime reservationAvailability)
        {
            return reservationAvailability >= _availableMinimum && reservationAvailability <= _availableMaximum;
        }
    }
```

- Our Customer class now has private methods which *further* increases the encapsulation of our object. Now outside clients have zero knowledge that such properties even exist in the first place. All these outside clients know is that there is a method they can call by passing a DateTime that will return a boolean that states if the customer is available or not at that time slot.

---
:heavy_check_mark: Every private field in an application should either be readonly or const to ensure they are not allowed to change after their initialization.

---

#### The Result of Moving Logic Down

- Our final Customer and ReservationObjects are a lot easier to understand afterwards because we have moved logic around to where it logically belongs.

**Figure 1-22** Final Customer class with method and encapsulated properties

```csharp
    public class CustomerWithPrivateValues
    {
        private readonly DateTime _availableMinimum;

        private readonly DateTime _availableMaximum;

        public CustomerWithPrivateValues(DateTime minAvailability, DateTime maxAvailability)
        {
            _availableMinimum = minAvailability;
            _availableMaximum = maxAvailability;
        }

        public bool AvailabilityMatchesCustomer(DateTime reservationAvailability)
        {
            return reservationAvailability >= _availableMinimum && reservationAvailability <= _availableMaximum;
        }
    }
```

#### The Ability to Test Easily Accessible Methods

- Now that our method is both private and the details of its implementation are encapsulated, testing is a straight forward process.

**Figure 1-23** Tests for our Customer class

```csharp
    [TestClass]
    public class CustomerTests
    {
        private readonly CustomerWithPrivateValues _customer;

        public CustomerTests()
        {
            var min = new DateTime(2000, 1, 1, 18, 0, 0);
            var max = new DateTime(2000, 1, 1, 19, 0, 0);
            _customer = new CustomerWithPrivateValues(min, max);
        }

        [TestMethod]
        public void AvailabilityMatchesCustomer_OutOfRange_ReturnsFalse()
        {
            var availability = new DateTime(2000, 1, 1, 17, 45, 0);

            var result = _customer.AvailabilityMatchesCustomer(availability);

            Assert.IsFalse(result);
        }

        [TestMethod]
        public void AvailabilityMatchesCustomer_InRange_ReturnsTrue()
        {
            var availability = new DateTime(2000, 1, 1, 18, 30, 0);

            var result = _customer.AvailabilityMatchesCustomer(availability);

            Assert.IsTrue(result);
        }
    }
```

---
:x: Avoid using DateTime.Now or DateTime.UtcNow in test code. Every time your test suite runs, the value will changes, leading to undefined behavior in your tests.

---

---
:warning: DateTime.Min and DateTime.Max may seem like better alternatives to DateTime.Now, but these values do not accurately represent a probable application state.

---

### The Problem with Naked Properties

- Some properties in an object may hold a loose association with other properties in the object. So in certain cases, this association is strong enough to warrant these properties to belong to their own object.

**Figure 1-24** Revising our Customer class with name properties

```csharp
public class Customer
    {
        // Certain properties omitted for brevity

        public Customer(string firstName, string lastName)
        {
            Validator.ValidNameFormat(firstName);
            Validator.ValidNameFormat(lastName);

            FirstName = firstName;
            LastName = lastName;
        }

        public string FirstName { get; }

        public string LastName { get; }
    }
```

- The issue is not readily apparent here, but in many situations a name is far more complicated with many more options and properties. A name is typically not just a first and last, but also a middle, a preferred Nickname and optional titles and suffixes.

**Figure 1-25** Common name honorifics

```csharp
    public enum Honorifics
    {
        None = 0,
        Mr = 1,
        Mrs = 2,
        Miss = 3,
        Ms = 4,
        Dr = 5,
        Dds = 6,
        Esq = 7,
    }
```

**Figure 1-26** Common name suffixes

```csharp
    public enum Suffix
    {
        None = 0,
        Sr = 1,
        Jr = 2,
        Second = 3,
        Third = 4,
        Fourth = 5,
        Fifth = 6,
        PhD = 7,
    }
```

- When we have to combine all of these properties together, we get a Customer object below that is already very complicated. When we have naked properties it is very easy to get large objects with dozens of properties.

**Figure 1-27** Common name properties

```csharp
    public class CustomerWithComplexName
    {
        public CustomerWithComplexName(string firstName, string lastName, string middleName, string preferredName, Honorifics honorifics, Suffix suffix)
        {
            Validator.ValidNameFormat(firstName);
            Validator.ValidNameFormat(lastName);
            Validator.ValidNameFormat(middleName);
            Validator.ValidNameFormat(preferredName);

            FirstName = firstName;
            MiddleName = middleName;
            LastName = lastName;
            PreferredName = preferredName;
            Honorifics = honorifics;
            Suffix = suffix;
        }

        public string FirstName { get; }

        public string MiddleName { get; set; }

        public string LastName { get; }

        public string PreferredName { get; set; }

        public Honorifics Honorifics { get; set; }

        public Suffix Suffix { get; set; }
    }
```

- Much like our other examples of how encapsulation, our Customer object should not be aware of how a name is structured or any operations on that name. All our Customer should know is that it has a name and that Name object has a certain set of operations available.

#### The Frustration with Testing Naked Properties and Collections

##### Step N3

#### The Result after Encapsulating Properties and Collections

## Hard Truth

### Coding is easy. Engineering is hard

Writing code and engineering software are two fundamentally different things. The end goal for both is the same. Both the engineer and the coder want a software solution that accomplishes the desired task in a reasonable amount of time. But the engineer and code get to the same conclusion through different methods. The engineer is retrospective as he works, drawing on past experiences, pausing to give thought, and contemplating choices. The coder, codes. He gets to a solution the fastest way possible. If the result works, then the ends have justified the means. The engineer understands that his work is a disciple. They must practice and refine their skills on a weekly basis. The coder looks at some quick documentation or sample code and attempts to piece together a solution. The engineer strives for simplicity everywhere. The coder chases the latest technology trends. The engineer understands that everything involves making a decision, acknowledging that trade-offs are required. The coder goes with their gut without contemplating advantages and disadvantages for each possible decision. Many people take the coding route. It is the path of least resistance.

> "Two roads diverged in a wood, / and I—
I took the one less traveled by, / And that has made all the difference." (Frost, lines 1-2)

What road will you take?

## Vocation

### Engineer is a mentality, not a title

Being an engineer derives from *wanting* to be an engineer. Software is different in that we do not (as of this book) not have hard standards for what qualifies as an engineer. Other engineering disciples have testing requirements and tests that one must pass before they can receive their qualifications. Software is more of a wild-west situation. It is difficult to separate the coders from the engineers. Wanting to write great software is just the start. The mentality of an engineer is to seek perfection, knowing your never get there-but always striving for the best.

## Automation

### Automate your decision making process

Automation is not limited to just menial tasks that involve your application. We must automate our decision making process. This saves us the time from going back and forth between possible solutions. By adopting a set of "engineering values", our decisions are made for us. It is up to us to coming up with our own principles, but I suggest the following while getting started.

1) Consistent over irregular
2) Simplicity over complexity
3) Testable over unverified
4) Automated over manual
5) Supported over niche

## Conclusion
