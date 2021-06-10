# Conceptual Ideas

### Chapter 6: Tests don't lie

#### Quote (something insurance)

The purpose of tests

- Tests provide clarity
- Tests provide confidence

#### Coding Horror Story

"I've had great success without unit testing".

Tests as a feedback loop

- Tests allow rapid change
- Test coverage is a lie

#### Lesson Learned

- XML Creation requiring Approvals testing - Chapter 6, Testing

#### Bad Engineering

- 737 MAX

Tests as insurance

- Is it tested
- Is it tested properly (conditional, Assert(1).Equals(1))
- Is it easy to test

#### Career Advice

Bad software hurts customers

- Poor software is under performing
- Poor software is harder to interact with
- Customers aren't stupid (people can tell your app is bad)
(story moving from android to apple because i could tell the
ios apps were just red-headed stepchild)

## Part 3: Common Issues

### Chapter 10: Testing

Not testing

- Self explanatory
(I've had great success without testing story)
- Unit tests are not enough

Not testing the right thing

- Every condition means a test

Testing the wrong thing

- Testing method calls
- Writing the same code in the test that you are testing

Overuse of mocks

- Mocks are a false sense of security

### Chapter 11: Encapsulation

Public setters

- Root of all evil
- Embrace Constructors
- Embrace static constructor methods for clarity

Excessive Properties

- Encapsulate object details
- Abstract primitive types (Name class vs 2 strings)

Private methods

- Second worse thing to public setters
- Push private methods down

Extension methods

- Purpose of extension methods
- Visible to all types
- Can't mock extension methods

Exposed collections

- Collections are arbitration
- Collections are naked

Leaky domain

- Clients don't need everything
- Embrace diverse responses
- Response objects allow for 1-M with clients

Entity Framework

- Encapsulation classes
- Embrace owned types
- Encapsulate collections

### Chapter 12: Coupling

The "new" keyword

- "new" is glue, no "new" is a weld

Concrete dependencies

- Don't marry concrete types
- Concrete types destroy testing
- Concrete types inhibit polymorphism

Friend classes

- Friend classes are the highest form of coupling
- (Internal in other languages)

Inheritance

- Inheritance is the second highest form of coupling
- O and L in SOLID are because of inheritance

Dependency Injection

- Relying on dispose  
- Injecting the container  
- Resolving from the container  
- Over injecting (5+ things in a class)
- Fancy injecting  (stupid shit you shouldn't good)
- Inappropriate registration
- Dependency Swallowing
- DI should be seen, but not herd

### DI Best Practice

- Single, Scoped, Instance

### Chapter 13: Repetition

Duplication in the presentation layer

- Controllers have one purpose
- Its the same process every time (validate, catch, log, return)
- Consolidate the process

Duplication in the data layer

- Data classes have one purpose
- Its the same process every time (all, one, add, update, remove)
- Consolidate the process

Checks everywhere

(functional opportunity)

- If/else/switch hell
- Fail early and throw

Logging everything

- Logging doesn't replace testing
- Logging is line noise

### Chapter 14: Responsibility

Too many conditional statements

- Minimize procedural code

Classes doing too much

- God classes

Methods doing too much

- Embrace events (sending an email after singing up)

Logic in the wrong place

- Reverse hour glass code
- Skinny at the top and bottom
- Fat in the middle (application, domain)
