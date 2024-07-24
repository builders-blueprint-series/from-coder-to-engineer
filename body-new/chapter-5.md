# Abstraction

<!-- Chapter Concepts -->
<!-- Logic in the wrong place - data fetch/Validation/logic in app -->
<!-- Excessive parameters - Command -->
<!-- Excessive dependencies - Chain of Responsibility -->

## Key Terms

- Abstraction
- Command
- Chain of Responsibility
- Layering

## Goals

After completing this chapter will you will be able to...

Grasp how placing certain responsibilities in different parts of our application leads to easier development.

Understand when parameters can be shrunk down to single objects to enforce encapsulation.

Learn how we can reduce service dependencies by utilizing a common pattern.

## Introduction

- Abstraction is a major topic in object-oriented programming.
- Abstraction is confusing at first because it shares common principles to Encapsulation.
- Abstraction is hiding how something is implemented vs hiding information.
- Maps are great examples of an abstraction because it gives us enough general knowledge, but doesn't fuss with unnecessary details.

## Wisdom

> "There isn’t a parallel of latitude but thinks it would have been the equator if it had had its rights." ― Mark Twain

## Simple Success - The Mercator Projection

The Mercator projection is a map of the world, but it is a certain type of projection. It gets its name from its creator, Gerardus Mercator from all the way back in 1569. The map is specifically designed to aide ships in navigation. It introduced many common attributes we associate with map today such as north being up, and the general preservation of shapes and sizes. Where the Mercator projection is different is that it preserves the course of any ship to any point in the map. A ship could start on any position on earth, sail in the exact heading the map indicated, and it would eventually end up where the map pointed to. It's main purpose was to preserve navigation courses. The main drawback of the map is that the closer you get to the poles, the more the land masses had to be enlarged. That is why greenland and antarctica look are lot bigger than they actually are. The Mercator projection is simple cause it has a sole singular purpose, to aid ships in navigation. It just happened to do everything else so well, it became one of the standard ways we have taught the general geography of the earth for centuries now.

## Code

### The Problem with Logic in the Wrong Place

- Most applications have three main functions. Persistence, transformation, and exposure of data.
- Each layer in your application should stay separate and sanitized against all other layers.
- Each part of your application should interact via interfaces or simple objects that do not allow details to seep or float into other layers that should not be privy to those details.

---

:large_blue_circle: One way to visualize layers in an application is to think of each as a project or separate DLL. This allows you to both mentally and physically separate them easily.

---

If you are new to the terms layers or boundaries, do not fret. Software engineering uses a lot of overloaded terms that can have the same meaning. We are primarily concerned with keeping logic in the correct place to enable proper automated testing.

---

:heavy_check_mark: When we separate our application into different layers, we are employing Dependency Inversion. Using interfaces as a primary means to communicate between the boundaries of our application.

---

#### Poorly abstracted Presentation layer exposes implementation details

- A poorly abstracted presentation layer allows for details to be exposed in certain sections of your code that should instead be abstracted away. In the code sample below, we are using a DBContext object to access a database directly in an API controller. The issue will be exposed when we try to write an automated test for this piece of code.

**Figure 5-x** A controller class for Reservations

```csharp
[ApiController]
[Route("[controller]")]
public class ReservationController : ControllerBase
{
    private readonly MyAppDbContext _dbContext;

    public ReservationController(MyAppDbContext dbContext)
    {
        _dbContext = dbContext;
    }

    [HttpGet(Name = "GetReservationById")]
    [ProducesResponseType(typeof(Reservation), StatusCodes.Status200OK)]
    public IActionResult ReservationById(Guid id)
    {
        var reservation = _dbContext.Reservations.FindById(id);

        return Ok(reservation);
    }
}
```

- In the sample above we have a simple API controller with one action that will retrieve a reservation by an identifier. The code is fully testable. The issue is that our presentation layer, the API, should have zero knowledge of _how_ an object is retrieved from persistence or how it is transformed. When any layer of your application is privy to details it should not be aware of-a bad abstraction is present. Bad abstractions will either inhibit or restrict future change in the application.

---

:warning: Do not assume that the specifics of your presentation layer will always stay the same. If you need to change from a REST API to gRPC or GraphQL later-modifying your code will be painful because you will need to change a lot more than if you had just dealt with a simple interface method.

---

- When we attempt to write an automated test for this section of code. It will be very difficult to do so.

**Figure 5-x** Test for the ReservationController

```csharp
[TestClass]
public class ReservationControllerTests
{
    [TestMethod]
    public void ReservationById_ReturnsCorrectAction()
    {
        var controller = new ReservationController(new MyAppDbContext());

        var result = controller.GetReservationById(Guid.NewGuid());

        Assert.IsInstanceOfType<OkObjectResult>(result);
    }
}
```

- This will technically work, however we are using our production DbContext to write an automated test. This is the last thing we want to be doing. We should not be using production data in automated tests. Any data we use should strictly be for test cases.

---
:warning: When we use a persistence method that is reading or writing to a non-volatile medium of storage such as a disk or file. That counts as an integration, not unit test.

---

- We need to introduce an interface between our presentation layer, the API, and the rest of our application. This will allow us to write automated tests for our presentation layer without.

#### Poorly abstracted Presentation layer exposes domain details

- If you look back at our controller, you will notice we are returning a domain entity directly from the database. This is not advised as there are multiple issues that may arise when directly returning database objects.

**Figure 5-x** A controller class for Reservations

```csharp
[ApiController]
[Route("[controller]")]
public class ReservationController : ControllerBase
{
    private readonly MyAppDbContext _dbContext;

    public ReservationController(MyAppDbContext dbContext)
    {
        _dbContext = dbContext;
    }

    [HttpGet(Name = "GetReservationById")]
    [ProducesResponseType(typeof(Reservation), StatusCodes.Status200OK)]
    public IActionResult ReservationById(Guid id)
    {
        var reservation = _dbContext.Reservations.FindById(id);

        return Ok(reservation);
    }
}
```

Some issues from directly returning a domain entity:

- Exposure of unnecessary data fields
- Exposure of the wrong data type
- Undefined behavior from non-aggregated database queries
- Non-flattened data structures

##### Exposure of Unnecessary Data Fields

- When we return domain objects directly from the database, we risk return data fields are that unnecessary to the client.

**Figure 5-x** The Reservation Class

```csharp
public class Reservation
{
    // some details removed for brevity

    public Guid Id { get; }

    public DateTime TimeFor { get; }

    public Name Name { get; }

    public bool IsSpecialOccasion { get; }
}
```

- What if our 

---

:large_blue_circle: The best way to divide a dotnet project into layers is to separate by projects. This allows us to specifically define the dependencies of each project.

---

#### Poorly abstracted Application layer exposes details

**Figure 4-x** A method for removing a reservation from persistence

```csharp
public class ReservationService
{
    private readonly IReservationRepository _repository;

    public ReservationService(IReservationRepository repository)
    {
        _repository = repository;
    }

    public int DeleteReservation(Guid id)
    {
        var success = _repository.DeleteReservation(id);

        if (success)
        {
            return StatusCodes.Status204NoContent;
        }

        return StatusCodes.Status500InternalServerError;
    }
}



```

- This example illustrates how details from the persistence layer in the form of REST status codes are in an application level service. Once again, from a testing standpoint-this code is perfectly acceptable to unit test.
- Just like how the presentation layer should not know how entities are persisted or transformed, the application layer should not be aware of how the data is finally returned to the client.

---

:warning: In the previous section, details from the application layer seeped up into the presentation layer. In this example, details are leaking down.

---

#### Poorly abstracted Infrastructure layer exposes details

---

:heavy_check_mark: Layers should communicate via interfaces. This keeps each layer abstracted from another and keeps testing simple and easy. Typically interfaces live in the same layer they are used in and are implemented in an separate project.

---

### The Problem with Excessive Parameters

#### Excessive Parameters Cascade Changes Down

### The Problem with Excessive Dependencies

#### Excessive Dependencies Hinder Testing
