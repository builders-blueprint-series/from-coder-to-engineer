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

**Figure 3-1** A customer class object that will be initialized via its constructor.

```csharp
public class Customer
{
    public Customer(string name)
    {
        Name = name;
    }

    public string Name { get; }
}
```

- Our code is tied to passing a string to this constructor every time we want to initialize it.

- This may not appear to be an issue currently. We know that using constructors is good because allows us to enforce object constraints.

- The main issue is that raw constructors inhibit future change.

**Figure 3-2** Calling our Customer constructor directly in our code.

```csharp
public void CreateCustomer(CreateCustomerRequest request)
{
    var customer = new Customer(request.Name);

    // Finish adding customer.
}
```

- We are bound to this constructor interface. Every time we want to create a Customer object we have to go through this specific interface.

- Even worse, we are exposing details that we care little about.

> :warning: Creating a new constructor for every possible situation you may need is not advised. We want to expose as few possibilities for object creation as possible.

#### Raw constructors are not declarative

- Constructors do not allow us to be declarative with what we are initializing. We may be creating a Customer-but constructors are unable to express potentially useful information.



#### Raw constructors inhibit future change

- There is highly probably situation where during the lifecycle of our application we need to update our customer object.

- We have new business requirements that allows customers to select a preferred seating arrangement.

**Figure 3-3** Updating our Customer object with a PreferredSeating enumeration.

```csharp
public enum PreferredSeating
{
    Table = 0,
    Booth = 1,
    Bar = 2,
}

public class UpdatedCustomer
{
    public UpdatedCustomer(string name, PreferredSeating preferredSeating)
    {
        Name = name;
        PreferredSeating = preferredSeating;
    }

    public string Name { get; }

    public PreferredSeating PreferredSeating { get; }
}
```

- We would now have to update EVERY constructor in our application since the interface of the object creation process has changed.

**Figure 3-4** New constructor call for updated property.

```csharp
public void UpdatedCreateCustomer(CreateCustomerRequest request)
{
    var customer = new UpdatedCustomer(request.Name, request.PreferredSeating);

    // Finish adding customer.
}
```

- Having to do this five, ten, or twenty times in our application would get old fast.

#### Transforming X to Y

##### Factory Method

##### Separate Factories

#### The Result of Y

#### The Ability to Test Y
