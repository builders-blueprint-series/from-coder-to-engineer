# Encapsulation

## Key Terms

Encapsulation

## Introduction

## Goals

After completing this chapter will you will be able to...

Understand how public setters breaks encapsulation.

Identity where methods belong and how access modifies affect the ability to test code.

Spot where naked collections and property can be consolidated.

## Wisdom

> "You're on a need-to-know basis, and you don't need to know." (Simpson, Bruckheimer, & Bay, 1996).

## The Success of Southwest Airlines - Simplicity In the Air

In the highly competitive and complicated world of air transportation, Southwest airlines has made a name for itself by flying against the air current of convention. Their business model is the definition of simplicity in the airline world. Southwest does not operate hubs, instead they fly mostly point-to-point. This eliminates the need for many travelers to connect. Less connections means passengers do not have to worry about a checked bag not making it to the next plane-or a weather delay causing a missed connection. They only operate one type of plane, the 737. They can save money by only needing to train, perform maintenance, and have pilots qualified for a single aircraft. They only have one type of seating, all coach. Southwest does not sell food on board, and they do not assign seats. In addition, they provide a simple bundled fare system to their customers. Southwest customers know exactly what they are getting when they buy a ticket. A simple, straight forward, no-frills experience that gets them to their destination. Great software is like Southwest airlines. Your end-users should get a consistent experience every time without any undue frustration.

## Code

### The Problem with Public Setters

#### Introduce topic of restaurant reservation system

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

The Customer class will initially represent customer who wish to dine in our restaurant.

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

- A class know what is the its ideal situation. Should not rely on outside influences.

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

#### Transforming Public Setters to Private

##### Step N1

#### The Result of Zero Public Setters

#### The Easiness of Testing a Closed Class

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
