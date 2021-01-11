# Conceptual Ideas

Introduction

- Software engineering is not black and white, its a sea a gray
- Software is knowing what not to do
- Only you know your application, so only you can make the best decision
- Bad code comes from either not knowing, or not caring, you can't know everything, but you can eliminate the second

## Part 1: An Engineering Mind

### Chapter 1: Coding is easy. Engineering is hard

Engineer is a mentality, not a title

- Good software comes from wanting to write good software (no silver bullet library, framework, architecture)
- Code is a by-product
- Remove yourself from your code
- Code and coders are cheap
- Engineers are rare

Titles and years of experience don't matter

- Titles mean nothing
- Years mean nothing
- You're only as good as the last thing you learned

Speak up now, or pay later

- Don't be timid about calling out bad software
- Precedence doesn't mean correctness
- Poor software proliferates exponentially

One line a day keeps the refactoring away

- Software is imperfect
- Software is never finished
- Software is entropic
- Fix one line a day (make this cultural)

### Chapter 2: Favor simplicity over complexity

The five minute rule

- Can you explain anything to a junior developer in five minutes
- Complex software doesn't mean complex code

Easier to understand

- Simple is easy to learn and memorize
- Simple is easy to onboard new team members

Easier to work with

- Simple is easy to change
- Simple is easy to add to

Associate lines with cost

- Everyone line of code costs money (not absolute)
- Every line adds complexity

Anything that you allow, will happen

- Every line of code sets a standard
- Cargo cult programming is the norm, not the exception

Frameworks aren't friends

- Frameworks hide problems
- Frameworks are one more thing you need to know
- You are responsible for all code in your application

### Chapter 3: Poorly written software harms everyone

Bad software hurts customers

- Poor software is under performing
- Poor software is harder to interact with
- Customers aren't stupid (people can tell your app is bad)

Bad software costs money

- Bad code requires more developers
- Bad code requires more time
(Story about adding more ram because of crashes)
- Bad code increases turnover

Bad software hurts developers

- Bad code is frustrating
- Bad code slows development
- Bad code hurts culture
- Bad software reinforces future bad software

Small things add up

- Bad code has no single underlying issue
- Small issues cause large ones
- Fix your broken windows

### Chapter 4: The Engineering Process

<!-- Find better part name -->

Software in an immature process

Software is not a physical science

- Story of Tacoma Narrows Bridge
(Toyota break controller, 737 max, florida pedestrian bridge)
- Software does not have a set of mathematical formulas determined by the laws of physics

Need to create your own laws

- Software requires you to enforce laws of good engineering

Automate the engineering process

- Embrace the compiler
- Embrace your test suite
- Embrace your dev ops process

Code Reviews Are Sacred

- Embrace code reviews as a feedback mechanism
- Be tough, but don't be mean
- Address issues in code review, or face the consequences later

## Part 2: Tools of the trade

<!-- Find better part name -->

### Chapter 5: You can't cheat the compiler

Compiler is the first line of defense

- Shortest feedback loop available
- The compiler will never lie
- Use the complier for enforce style
- Pick a style and stay consistent

Embracing the compiler

- Style vs Best Practices
- ESLint, EditorConfigs, StyleGuides, Prettier, Warnings As Errors, Roselyn Analyzers
- Encapsulation

Turn lemmings into lemonade

- Embrace cargo cult programming to your advantage

### Chapter 6: Tests don't lie

The purpose of tests

<!-- Automatic tests can't find everything -->

- Tests provide clarity
- Tests provide confidence

Tests as a feedback loop

- Tests allow rapid change
- Test coverage is a lie

Tests as insurance

- Is it tested
- Is it tested properly
- Is it easy to test

### Chapter 7: Coding building blocks

Object Oriented Code

- Purpose of encapsulation
- Purpose of inheritance
- Purpose of polymorphism (big daddy)
- Purpose of abstraction

Functional Code

- Purpose of functional code (minimize procedural)
- OOP and Functional aren't mutually exclusive
- Methods vs Classes
- Purpose of callbacks

### Chapter 8: Principles to live by

Don't do too much

- Purpose of Single Responsibility
- SRP versus DRY

Interfaces over inheritance

- Purpose of Open/Closed
- Purpose of Liskov

Only provide what is needed

- Purpose of Interface Segregation

Ideas over implementations

- Purpose of Dependency Inversion (relate back to polymorphism)
- DI vs DI (dependency injection) vs DI (inversion of control)

### Chapter 9: Patterns to remember

The purpose of design patterns

- Patterns make life easier
- Patterns are just an extreme form of polymorphism
- You can't improve sliced bread, so embrace it

There are no such things as anti-patterns

- Every pattern has a time and place
- Singleton, Service Locator, ect.

Knowing where is more important than how

- Don't memorize patterns, memorize when to use them
- Patterns play nicely with each other

Getting comfortable with patterns

- Creational Patterns Overview
- Structural Patterns Overview
- Behavior Patterns Overview
- Builder Pattern (Dapper, SQL query, Predicates)
- Chain of Responsibility (Complex processes, service call chain)
- Command (Common interface)
- Mediator (Service SRP)
- Observer (Conditional replacement)
- Strategy (Conditional replacement)

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
- DI should be seen, but not herd

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

### Chapter 15: Miscellaneous

Comments

- Comments describe why not how

Naming

- Style

Asynchronous

- Understand what goes on with async
- The amount of code generated
