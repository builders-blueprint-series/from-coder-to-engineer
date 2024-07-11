# Contents at a glance

1. Encapsulation
   1. Public setters
   2. Private methods
   3. Naked properties
2. Inheritance
   1. Multiple Implementations
   2. Bad Base Classes - Liskov (pre/post-conditions/invariants, use interfaces)
      (single class does too much)
      * Break pre-condition (strengthen, require special condition for status)
      * Break post-condition (weaken, allow elites to book with no availability)
      * (Second highest form of coupling)
   3. Unimplemented Interfaces - Segregate Interfaces
      * Inconsistent customer API
      * Bad abstraction
3. Coupling
   1. "new" keyword - Factory, Factory method
      * Raw constructors are not declarative
      * Raw constructors may be changed multiple times
      * Raw constructors don't return anything
   2. Static - Bad vs Good, Loggers everywhere
      * Good vs Bad static
      * Factories can be static (no private fields)
      * Utility functions can be static
      * Instance members make bad static
      * Class members depends
   3. Friendship/Internal
      * Friendship breaks encapsulation (highest form of coupling)
      * Friendship is a bad abstraction
4. Polymorphism
   1. Bad Dependencies - Dependency Injection
   2. Relying on implementations - Dependency Inversion (DateTime, HttpClient, DbContext, SqlConnection, Loggers, SOAP Client Factories)
   3. Small Variations - Generics & Constraints
5. Abstraction
   1. Logic in the wrong place - everything data fetch/Validation/logic in app
   2. Excessive parameters - Command
   3. Excessive dependencies - Chain of Responsibility
6. Responsibility
   1. God classes - Observer (just send the event, push into domain in ch. 8)
   2. Complex creations - Builder
   3. Complex procedural code - Strategy
7. Common Interfaces
   1. Responses - Envelope (null is a bad abstraction, exceptions are not control flow, update constructor validation with base Valid attribute)
   2. Services - Mediator
   3. Similar Queries - Query Object
8. Repetition
   1. Repetition in the presentation layer - Common Interface
   2. Repetition in the application layer - Common Interface (base handlers, base chains, domain services, events to repo/aggregate root)
   3. Repetition in the data layer - Common Interface

## Quotes

"Brevity is the soul of wit." - Polonius, Hamlet

"Everything should as simple as possible, but not simpler."

"The enemy of perfection is good enough."

## Working Title

Builder's Blueprint:

From Code to Software - Golden Gate
From Code to Components - Brooklyn
From Code to Patterns - Tower
From Code to Architecture - Pont du Gard
From Code to Clouds - Coronado/Sunshine Skyway
From Code to Structure - Four pillars of software
From Code to Domains - Move data bags to DDD

![Bridge](title_cover.png)

## Horror Stories

"Can't tell you the last time we deployed to production."

Emphasize moving from code to software

Tests are insurance. Your code gets so bad that an insurance company refuses to cover you.
Your code is so bad that writing and maintaining tests is almost impossible.

## DI Topics

Injecting the Service Container
Resolving from the Service Container
Using Statements as "DI"
Injection other than constructor
[FromServices] Tag
Fancy Injection - anything other than give me interface (configuration is exception)
Fancy Registration - anything other than this equals that
Service Locators in application code
Writing your own DI container
Modifying the existing DI container
Swallowing registrations - singleton accepting transient
Bad registration - not knowing how to register things
Injecting excessive dependencies 5+
Null checks on injected services
