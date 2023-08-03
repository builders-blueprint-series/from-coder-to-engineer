# Coupling

## Key Terms

- Friendship
- Internal
- Static

## Goals

After completing this chapter will you will be able to...

Learn why the naked creation of objects limits your ability to change in the future.

Understand where static properties and functions may be appropriate can be useful.

Realize how internal and friendship breaks the encapsulation of your application.

## Introduction

- The previous chapters are concerned with how objects can relate to each other
- Coupling primarily deals with how object are created or used
- Chapter 3 deals with the "new" keyword-the de-facto way off object creation. The use of "new" has correct and incorrect usages
- Static is an alternative to the standard way of scoping to an object
- The ability to friend or internalize an object allows you to share private data innately

## Wisdom

The purpose of abstraction is not to be vague, but to create a new semantic level in which one can be absolutely precise - Edsger Dijkstra

## Code

### The Problem with the "new" keyword

#### The "new" keyword is glue

- The use of the "new" keyword is an _explicit_ contract between the client (caller) and implementation (creation) that glues the caller to the constructor contract.

**Figure 3-1** A reservation class object that will be initialized via its constructor.

```csharp
public class Reservation
{
    public Reservation(int CustomerCount, DateTime ReservationTime)
    {
        // Initialize properties
    }
}
```

- Our code is tied to passing an integer and DateTime to this constructor every time we want to initialize it.

**Figure 3-2** Calling our Reservation constructor directly in our code.

```csharp
public class ReservationService
{
    public void Handle(ReservationRequest request)
    {
        var reservation = new Reservation(request.CustomerCount, request.ReservationTime);

        // Finish reservation process.
    }
}
```

#### Raw constructors are not declarative

#### Raw constructors inhibit future change

#### Transforming X to Y

##### Factory Method

##### Separate Factories

#### The Result of Y

#### The Ability to Test Y
