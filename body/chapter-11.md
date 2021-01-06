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

## Introduction

1. Encapsulation as a foundation for good coding techniques

2. Encapsulation issues compound together

3. Properly encapsulated code results in better testing

4. Properly encapsulated code limits options, shortens the feedback loop, and enforces simplicity through a common interface

## Problem Statement

---

1. Continuation of reservation system introduced in the previous chapter.

<br/>

**Listing 11-X** Customer object with properties

```csharp
    public class Customer
    {
        public Guid Id { get; set; }

        public string FirstName { get; set; }

        public string LastName { get; set; }

        public string Email { get; set; }

        public string LoyaltyId { get; set; }

        public int LoyaltyPoints { get; set; }

        public int PointsYearToDate { get; set; }

        public LoyaltyStatus Status { get; set; }
    }
```

**Listing 11-X** Loyalty Status enumeration

```csharp
    public enum LoyaltyStatus
    {
        Blue,
        Silver,
        Gold,
        Platinum,
    }
```

Our Customer class contains a handful of properties related to their identity and their loyalty status. Our status is defined as an enumeration.

<br/>

**Listing** 11-X Customer Service

```csharp
    public class CustomerService : ICustomerService
    {
        private readonly ICustomerRepository _customerRepository;
        private readonly IEmailClient _emailClient;

        public CustomerService(ICustomerRepository customerRepository, IEmailClient emailClient)
        {
            _customerRepository = customerRepository;
            _emailClient = emailClient;
        }

        public void AddLoyaltyPoints(Guid customerId, int loyaltyPoints)
        {
            var customer = _customerRepository.GetById(customerId);

            var newStatus = AddPointsAndCheckForNewStatus(customer, loyaltyPoints);

            if (newStatus)
            {
                _emailClient.SendNewStatusEmail(customer);
            }

            _customerRepository.UpdateCustomer(customer);
        }

        public void GiftPoints(Guid customerId, int loyaltyPoints)
        {
            var customer = _customerRepository.GetById(customerId);

            AddPointsAndCheckForNewStatus(customer, loyaltyPoints);

            _emailClient.SendApology(customer);

            _customerRepository.UpdateCustomer(customer);
        }

        public Customer CreateCustomer(string firstName, string lastName, string email)
        {
            var customer = new Customer
            {
                Id = Guid.NewGuid(),
                FirstName = firstName,
                LastName = lastName,
                Email = email,
                LoyaltyPoints = 0,
                PointsYearToDate = 0,
                Status = LoyaltyStatus.Blue,
            };

            customer.LoyaltyId = customer.Id.GenerateCustomerId();

            _customerRepository.AddCustomer(customer);

            return customer;
        }

        public Customer StatusMatchCustomer(string firstName, string lastName, string email, LoyaltyStatus status)
        {
            var customer = new Customer
            {
                Id = Guid.NewGuid(),
                FirstName = firstName,
                LastName = lastName,
                Email = email,
                LoyaltyPoints = 0,
                PointsYearToDate = 0,
                Status = status,
            };

            customer.LoyaltyId = customer.Id.GenerateCustomerId();

            _customerRepository.AddCustomer(customer);

            return customer;
        }

        public IEnumerable<Customer> GetAllSilverCustomers()
        {
            var allCustomers = _customerRepository.GetAllCustomers();

            return allCustomers.Where(customer => customer.Status == LoyaltyStatus.Silver);
        }

        private bool AddPointsAndCheckForNewStatus(Customer customer, int points)
        {
            var currentStatus = customer.Status;

            customer.LoyaltyPoints += points;
            customer.PointsYearToDate += points;

            if (customer.PointsYearToDate >= 25000)
            {
                customer.Status = LoyaltyStatus.Silver;
            }

            if (customer.PointsYearToDate >= 50000)
            {
                customer.Status = LoyaltyStatus.Gold;
            }

            if (customer.PointsYearToDate >= 75000)
            {
                customer.Status = LoyaltyStatus.Platinum;
            }

            if (currentStatus != customer.Status)
            {
                return true;
            }

            return false;
        }
    }
```

Our Customer Service contains a handful of methods relating to operations such as creating new Customers, adding points to their accounts for activity, and querying for certain customers.

<br/>

> **Note:** In a production situation, this class would have many more methods.

<!-- This should probably be expanded some more. -->

<br/>

**Listing 11-X** Customer Service Tests

```csharp
    [TestClass]
    public class CustomerServiceTests
    {
        private readonly Mock<ICustomerRepository> _customerRepository;
        private readonly Mock<IEmailClient> _emailClient;
        private readonly ICustomerService _customerService;

        public CustomerServiceTests()
        {
            _customerRepository = new Mock<ICustomerRepository>();
            _emailClient = new Mock<IEmailClient>();
            _emailClient.Setup(x => x.SendNewStatusEmail(It.IsAny<Customer>()));
            _customerService = new CustomerService(_customerRepository.Object, _emailClient.Object);
        }

        [TestMethod]
        public void AddLoyaltyPoints_NewStatus_SendsEmail()
        {
            _customerRepository.Setup(x => x.GetById(It.IsAny<Guid>()))
                .Returns(new Customer());

            _customerService.AddLoyaltyPoints(Guid.NewGuid(), 25000);

            _emailClient.Verify(x => x.SendNewStatusEmail(It.IsAny<Customer>()), Times.Once);
        }

        [TestMethod]
        public void AddLoyaltyPoints_NoNewStatus_DoesNotSendEmail()
        {
            _customerRepository.Setup(x => x.GetById(It.IsAny<Guid>()))
                .Returns(new Customer());

            _customerService.AddLoyaltyPoints(Guid.NewGuid(), 15000);

            _emailClient.Verify(x => x.SendNewStatusEmail(It.IsAny<Customer>()), Times.Never);
        }

        [TestMethod]
        public void GiftPoints_SendsApology()
        {
            _customerRepository.Setup(x => x.GetById(It.IsAny<Guid>()))
                .Returns(new Customer());

            _customerService.GiftPoints(Guid.NewGuid(), 5000);

            _emailClient.Verify(x => x.SendApology(It.IsAny<Customer>()), Times.Once);
        }

        [TestMethod]
        public void CreateCustomer_ReturnsCorrectValues()
        {
            const string firstName = "John";
            const string lastName = "Doe";
            const string email = "john@doe.com";

            var result = _customerService.CreateCustomer(firstName, lastName, email);

            Assert.AreEqual(firstName, result.FirstName);
            Assert.AreEqual(lastName, result.LastName);
            Assert.AreEqual(email, result.Email);
        }

        [TestMethod]
        public void StatusMatchCustomer_ReturnsCorrectValues()
        {
            const string firstName = "John";
            const string lastName = "Doe";
            const string email = "john@doe.com";
            const LoyaltyStatus status = LoyaltyStatus.Gold;

            var result = _customerService.StatusMatchCustomer(firstName, lastName, email, status);

            Assert.AreEqual(firstName, result.FirstName);
            Assert.AreEqual(lastName, result.LastName);
            Assert.AreEqual(email, result.Email);
            Assert.AreEqual(status, result.Status);
        }

        [TestMethod]
        public void GetAllSilverCustomers_ReturnsCorrectCustomers()
        {
            _customerRepository.Setup(x => x.GetAllCustomers())
                .Returns(new List<Customer>
                {
                    new Customer { Status = LoyaltyStatus.Silver },
                    new Customer { Status = LoyaltyStatus.Blue },
                    new Customer { Status = LoyaltyStatus.Silver },
                    new Customer { Status = LoyaltyStatus.Gold },
                });

            var result = _customerService.GetAllSilverCustomers();

            Assert.IsTrue(result.All(x => x.Status == LoyaltyStatus.Silver));
        }
    }
```

Our Customer Service tests cover unit level testing for our service. For mocking, I have utilized the popular Moq testing library.

<br/>

> **Note:** This service still requires a suite of integration tests to be considered complete. I have omitted them because I want to focus on the code contained in the service, not how it is being persisted.

<br/>

## Issues At Hand

---

<br/>

### Public Setters

#### Revisiting the Customer Class

**Listing 11-X** Customer class

```csharp
public class Customer
{
    public Guid Id { get; set; }

    public string FirstName { get; set; }

    public string LastName { get; set; }

    public string Email { get; set; }

    public string LoyaltyId { get; set; }

    public int LoyaltyPoints { get; set; }

    public int PointsYearToDate { get; set; }

    public LoyaltyStatus Status { get; set; }      
}
```

Code details

<br/>

#### Problems with Public Setters

---
<br/>

##### Exposed Properties

1. Introduction

###### Real Life Encapsulation

1. SSN number is private to you and only you

###### Objects mimic real life interactions

1. Objects are interfaces
2. Objects that limit details shorten the feedback loop

<br/>

##### Unprotected Invariants

1. Scattered Validation introduction

**Listing 11-X** Customer validation in service

Code details

**Listing 11-X** Customer validation in repository

Code details

###### Validation Statements require extra testing

1. Validation statements are no different than other conditionals

> **Note**: Every conditional statement you write means at least two tests, one per condition. The best way to not write these tests is to avoid conditional statements when possible.

###### State can not be guaranteed

**Listing 11-X** State in first Customer

Code details

**Listing 11-X** State in second Customer

Code details

1. Checking state causes extra tests

###### Objects are masters of their information

1. Objects should self-validate
2. Objects should be aware their state at all times
3. Fewer state options translates to fewer tests and bugs

<br/>

##### Non-centralized creation

###### Object changes cascade throughout application

> **Note:** What is Shotgun Surgery

**Listing 11-X** Customer has property added to it

Code details

1. Customer creation must be updated in entire code base

2. Objects should limit their creation options

3. Centralized object creation produces a common interface

<br/>

#### Solutions

---

<br/>

##### Converting to private setters

**Listing 11-X** Customer now has private setters

Code details

<br/>

##### Embracing Constructors

**Listing 11-X** Customer now has constructor

Code details

1. Constructors are common interfaces

2. Clarity through static methods

**Listing 11-X** Customer created via new acquisition

Code details

**Listing 11-X** Customer created via status match

Code details

##### Static methods provide intent and clarity

1. Static methods as a means to document intent
2. Static methods to choke parameter lists

> **Note**: Parameter hiding

<br/>

#### Result

---

<br/>

##### Improved testing

1. Code is easier to maintain
2. Code coverage has increased

<br/>

#### Conclusion

1. Public setters create an endless amount of possibilities for object creation and state, resulting in extraneous code
2. Private setters with constructors limit these possibilities, resulting in less code, fewer bugs, and a simplified code base
3. Static construction methods provide clarity for objects with confusing or excessive constructors.

<br/>

### Private Methods

<br/>

#### Issues At Hand

---

1. Revisiting the Customer Service class

**Listing 11-X** Customer Service class

#### Private methods can't be tested

#### Private methods indicate poorly placed code

#### Pushing private methods down

#### Improved testing

#### Conclusion

### Extension Methods

1. Some things require extension methods

2. Show code with improper extension methods

3. Extension methods are hard to mock

4. Extension methods have to be static

5. Extension methods are bound to all types

6. Show how to move extension methods into classes

7. Code can now be tested properly

8. Section conclusion

### Exposed Collections

1. Show class that returns an exposed collection, where logic is
performed on collection afterwards

2. Show why exposed collections make testing harder

3. Exposed collections can be changed at any time

4. Exposed collections don't convey meaning

5. Show how to move collections into dedicated type

6. Code can now be tested properly

7. Section conclusion

### Leaky Domain Logic

1. Show domain classes that have leaked to the presentation

2. Explain why clients don't care about your domain

3. Show why changing the domain forces clients to adapt

4. Show how having specified responses for the same domain allow for simplified code

5. Show how to move domain objects into specific responses

6. Code is now easier to change

7. Section conclusion

### Encapsulation with Entity Framework

1. Entity Framework means tradeoffs with encapsulation

2. Basic encapsulation with Entity Framework

3. EFCore allows for primitives to be fully encapsulated

4. Creating Value types in EFCore

5. Complex types require certain compromises

6. Encapsulating collections in EFCore

7. Section conclusion

## Limits of Encapsulation

1. Complete encapsulation of an application is not the goal.

2. Situations exist where the benefits and disadvantages of encapsulation needs to be weighed. A well-engineered application enforces encapsulation where it needs to.

3. Model Binding, Open API (Swagger), and Entity Framework are situations where the rules of encapsulation can be relaxed.

## Conclusion

1. Proper encapsulation is the foundation to well-engineered software.

2. Start with removing public setters and private methods from your application.

3. Eventually tackle larger issues with collections, misplaced domain objects and extension methods.

4. If you can only do one thing well in your application, encapsulate.
