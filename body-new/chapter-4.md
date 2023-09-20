# Abstraction

## Key Terms

- Command
- Chain of Responsibility
- Layering

## Goals

After completing this chapter will you will be able to...

Grasp how placing certain responsibilities in different parts of our application leads to easier development.

Understand when parameters can be shrunk down to single objects to enforce encapsulation.

Learn how we can reduce service dependencies by utilizing a common pattern.

## Introduction

- Abstraction is the final main topic in object-oriented programming.
- Abstraction is confusing at first because it shares common principles to Encapsulation.
- Abstraction is hiding how something is implemented vs hiding information.
- Maps are great examples of an abstraction because it gives us enough general knowledge, but doesn't fuss with unnecessary details.

## Wisdom

> "There isn’t a parallel of latitude but thinks it would have been the equator if it had had its rights." ― Mark Twain

## Simple Success - The Mercator Projection

## Code

### The Problem with Logic in the Wrong Place

- Most applications have three main functions. Persistence, transformation, and exposure of data.
- Each layer in your application should stay separate and sanitized against all other layers.
- Each part of your application should interact via interfaces or simple objects that do not allow details to seep or float into other layers that should not be privy to those details.

---
:large_blue_circle: One way to visualize layers in an application is to think of each as a project or separate dll. This allows you to both mentally and physically separate them easily.

---

#### Poorly abstracted Application layer inhibits testing

**Figure 4-x** A controller class for Reservations

```csharp
[Route("[controller]")]
[ApiController]
public class ReservationController : ControllerBase
{
    private readonly IReservationRepository _repository;

    public ReservationController(IReservationRepository repository)
    {
        _repository = repository;
    }

    [HttpGet(Name = "GetReservationById")]
    [ProducesResponseType(typeof(ReservationResponse), StatusCodes.Status200OK)]
    public IActionResult ReservationById(Guid id)
    {
        var reservation = _repository.GetById(id);

        var response = ReservationFactory.FromReservation(reservation);

        return Ok(response);
    }
}
```

- In the sample above we have a simple API controller with one action that will retrieve a reservation by an identifier. The code is fully testable.
- The issue is that our presentation layer, the API, should have zero knowledge of *how* an object is retrieved from persistence or how it is transformed.
- When any layer of your application is privy to details it should not be aware of-a bad abstraction is present. Bad abstractions will either inhibit or restrict future change in the application.

---
:warning: Do not assume that the specifics of your presentation layer will always stay the same. If you need to change from a REST API to gRPC or GraphQL later-modifying your code will be painful because you will need to change a lot more than if you had just dealt with a simple interface method.

---

#### Poorly abstracted Presentation layer exposes details

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
