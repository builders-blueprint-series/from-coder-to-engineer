# Responsibility

## Key Terms

- Observer
- Single Responsibility Principle
- Builder
- Strategy

## Goals

After completing this chapter will you will be able to...

Understand the nuances between balance responsibility and duplication in code.

Continue to understand how logic can be pushed down into the domain.

Learn how large objects built at runtime can be broken up into smaller pieces.

Apply object-oriented concepts to traditionally procedural code.

## Introduction

## Wisdom

## Simple Success

### The Problem with God Classes

- God classes are considered objects that have too many responsibilities.
- They may be considered testable where all branches have coverage. But the amount of tests required per class may be too high.
- Traditionally God classes form when they are performing logic that should be done by another object or performed in a different layer.

#### God classes performing after-effects

- A God class that performs side-effects as the result of an operation adds to the complexity of the test case.

**Figure 5-x** An extra branch as the result of a side-effect call

```csharp
public class ReservationService : IReservationService
{
    private readonly ISmtpClient _smtpClient;
    private readonly ICustomerRepository _customerRepository;
    private readonly IReservationRepository _reservationRepository;

    public ReservationService(ISmtpClient smtpClient, ICustomerRepository customerRepository, IReservationRepository reservationRepository)
    {
        _smtpClient = smtpClient;
        _customerRepository = customerRepository;
        _reservationRepository = reservationRepository;
    }

    public ReservationResult CreateReservation(CreateReservationRequest request)
    {
        var reservation = ReservationFactory.FromRequest(request);

        var addResult = _reservationRepository.AddReservation(reservation);

        var customer = _customerRepository.FindById(request.CustomerId);

        if (addResult)
        {
            _smtpClient.SendEmail(customer.Email, "Reservation Success", $"Your reservation is confirmed for {request.Timestamp}.");
        }

        return ReservationFactory.ReservationResult(addResult, customer);
    }
}
```

- Our application service has knowledge about details about what happens after the creation of a reservation.

- The process of sending an email after the creation of a reservation is a side-effect that does not need to be in our application service.

---

:x: In reality, many SMTP calls have HTML elements in them. HTML certainly does not belong in our application services.

---

#### Added complexity of testing God classes

- Our tests for this service method require us to verify two possible branches from the result of trying to create the reservation.

**Figure 5-x** Tests for the CreateReservation method

```csharp
[TestClass]
public class ReservationServiceTests
{
    private readonly Mock<ISmtpClient> _smptClient;
    private readonly Mock<ICustomerRepository> _customerRepository;
    private readonly Mock<IReservationRepository> _reservationRepository;
    private readonly IReservationService _service;

    public ReservationServiceTests()
    {
        _smptClient = new Mock<ISmtpClient>();
        _customerRepository = new Mock<ICustomerRepository>();
        _reservationRepository = new Mock<IReservationRepository>();
        _service = new ReservationService(_smptClient.Object, _customerRepository.Object, _reservationRepository.Object);
    }

    [TestMethod]
    public void CreateReservation_Success_CallsSmtpClient()
    {
        _reservationRepository.Setup(x => x.AddReservation(It.IsAny<Reservation>()))
            .Returns(true);

        _customerRepository.Setup(x => x.FindById(It.IsAny<Guid>()))
            .Returns(new Customer());

        var result = _service.CreateReservation(new CreateReservationRequest());

        _smptClient.Verify(x => x.SendEmail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>()), Times.Once);
    }

    [TestMethod]
    public void CreateReservation_Failure_DoesNotCallSmtpClient()
    {
        _reservationRepository.Setup(x => x.AddReservation(It.IsAny<Reservation>()))
            .Returns(false);

        _customerRepository.Setup(x => x.FindById(It.IsAny<Guid>()))
            .Returns(new Customer());

        var result = _service.CreateReservation(new CreateReservationRequest());

        _smptClient.Verify(x => x.SendEmail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>()), Times.Never);
    }
}
```

- Our tests for our CreateReservation method have two main logical branches-one for a successful reservation creation, and one for failure.

- If there was a way to move our logic to a different area of our application we could simplify or eliminate our tests.

#### Transforming side-effect God classes

- Our biggest issue is the application service knowing about the email creation process. Not only do we want to remove the service from any knowledge about email, but all future possible side-effects that may occur in our application.

##### Step 1: Moving to Events

- One pattern we can use to handle side-effects in our application is to employ the Observer pattern, commonly referred to as an Event.

- Events are fire-and-forget. Meaning they do not return anything, and have a one-to-many relationship. One event can trigger multiple actions. The advantage of events, is that the sender, or publisher has zero knowledge about how many clients an event has, or any details about a client.

- The logical place to store events will be in our domain objects. Since most events are generated when an object is created, modified, or deleted-events can be created alongside of these method calls.

- All of our events will inherit the INotification interface. This will allows us to identify easily which objects are events so they we can constrain them later.

**Figure 5-x** The INotification interface

```csharp
public interface INotification
{
}
```

- You may add any relevant properties such as a timestamp or metadata you see fit to the interface that you believe all events should carry. For now, we will keep our interface empty.

---

:large_blue_circle: We are using this naming scheme because "event" is a keyword in C#. Although you may always add an "@" symbol in front of the keyword if you wish to use one verbatim.

---

- Because our entities will all be able to publish events, we need to add the necessary code to store them in our base Entity class.

**Figure 5-x** Entity base class updated for notifications

```csharp
public abstract class Entity
{
    private readonly ICollection<INotification> _notifications;

    protected Entity()
    {
        _notifications = new List<INotification>();
    }

    public IReadOnlyList<INotification> Notifications => _notifications.ToList();

    protected void AddNotification(INotification notification)
    {
        if (notification == null)
        {
            throw new NullReferenceException(nameof(notification));
        }

        _notifications.Add(notification);
    }
}
```

- Our base Entity class has a private field that contains a list of notifications with a public property to return a readonly version of said list. There is also a method for adding notifications.

---

:heavy_check_mark: Our method for adding notifications is protected. This ensures that only domain objects are allowed to create notifications-thus furthering our quest for encapsulation.

---

- Notification objects are plain by design that only contain the necessary metadata required for an event handler to function.

**Figures 5-x** The ReservationCreated notification object

```csharp
public class ReservationCreated : INotification
{
    public ReservationCreated(Guid customerId, DateTime timestamp)
    {
        CustomerId = customerId;
        Timestamp = timestamp;
    }

    public Guid CustomerId { get; }

    public DateTime Timestamp { get; }
}
```

---

:heavy_check_mark: Notifications should be named using the past tense. "Created", "Modified", or "Removed" are good starter ideas.

---

---

:x: Notifications should have no methods or functions. They should always be plain objects that exist only to carry the necessary properties to their respective handlers.

---


##### Step 2: Updating our domain object

- The responsibility for the creation of events will be in our domain objects.

**Figure 5-x** Updated reservation object with event creation

```csharp

```