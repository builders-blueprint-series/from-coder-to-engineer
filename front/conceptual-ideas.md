# Conceptual Ideas

## Part 1: An Engineering Mind

### Chapter 1: Coding is easy. Engineering is hard

#### Introduction

- Software engineering is not black and white, its a sea a gray
- Software is knowing what not to do
- Bad code comes from either not knowing, or not caring, you can't know everything, but you can eliminate the second

#### Quote (mentality is everything)

"Brevity is the soul of wit." - Polonius, Hamlet

#### Engineer is a mentality, not a title

- Good software comes from wanting to write good software
- No silver bullet library, framework, architecture
- Code is a by-product
- Remove yourself from your code
- Code and coders are cheap, engineers are rare and valuable
- Engineers think long term

#### Horror Story

- People love to over-engineer almost everything
- The Zen of Python (we like simple)

#### Titles and years of experience don't matter

- Titles mean nothing
- Years mean nothing
- You're only as good as the last thing you learned
- Don't be cocky, but don't accept something "just because"
- Good engineers are humble and let their work speak for themselves

#### Real-Life Example

- Southwest Airlines
- Simple, one plane, class, point to point, easy fare structure

#### Speak up now, or pay later

- Don't be timid about calling out bad software
- Precedence doesn't mean correctness
- Poor software proliferates exponentially

#### Lesson Learned

- Software is commercial art. It has to perform its function, and look nice in the process
- No one will live in a building that is ugly, or structurally unstable.

#### Did you know?

- There is such a thing as "feeling it" in software. Remember, commercial ART.

#### Conclusion

- Software is hard. So don't be so hard on yourself
- When in doubt, choose the simple way of doing things
- Think like an engineer, not a coder

### Chapter 2: Embrace the compiler. Save the code review

#### Introduction

- Automation is your friend for menial tasks
- The compiler is a good guy
- Other forms of automation such as test suites, and dev ops are good

#### Quote (don't do anything someone else can do for you)

#### Compiler is the first line of defense

- Shortest feedback loop available
- The compiler will never lie
- Use the complier for enforce style
- Pick a style and stay consistent

#### Horror Story

- "We'll catch it in code review"

#### Using the compiler

- Style vs Best Practices
- ESLint, EditorConfigs, StyleGuides, Prettier, Warnings As Errors, Roselyn Analyzers
- Encapsulation
- Use the plugins, ReSharper

#### Real-Life Engineering Disaster

- Tacoma Narrows Bridge

#### Lesson Learned

- Don't take things personally
- Own up to your mistakes
- Use failures as lessons

#### Automate everything

- Embrace the compiler
- Embrace your test suite
- Embrace your dev ops process
- Embrace cargo cult programming to your advantage

#### Career Advice

- Find a mentor who you can bounce ideas and questions off of!
- Find someone who will challenge your ideas and make you better
- This book would not have been possible without the countless people who gave input and helped edit

#### Code Reviews Are Sacred

- Code reviews are terrible at finding bad code
- Code reviews should be seen as a last resort
- Embrace code reviews as a feedback mechanism
- Be tough, but don't be mean
- Address issues in code review, or face the consequences later

#### Career Advice

Anything that you allow, will happen

- Every line of code sets a standard
- Cargo cult programming is the norm, not the exception

#### Food for thought

- It's your application, only you know what is truly the best solution
- What works for others may be wrong for you, and visa-versa.

#### Conclusion

- Automation saves time
- Automation will save your sanity

### Chapter 3: Development Fundamentals

#### Introduction

### Encapsulation

#### Quote (need to know basis)

#### Inheritance

#### Quote (get all the good, and all the bad)

#### Polymorphism

#### Quote

#### Abstraction

#### Quote (details are boring anything)

#### Quote

### Functional Programming

#### Minimize Procedural Code

#### Method vs Classes

#### Harnessing Callbacks

#### Conclusion

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
- Is it tested properly (conditional, Assert(1).Equals(1))
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
- Purpose of callbacks (polymorphism of functional)

### Chapter 8: Principles to live by

Don't do too much

- Purpose of Single Responsibility
- SRP versus DRY (aren't mutually exclusive)

Interfaces over inheritance

- Keep inheritance limited (1-2 max)
- Example of introducing an interface over base class
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
- Patterns play nicely with each other (pattern composition)

Getting comfortable with patterns

(forward-facing, active users)

- Finding a common interface
(n-1, IEnumerable, Func vs Action)

Patterns in detail

- Creational Patterns Overview (your di framework does it)
- Structural Patterns Overview (more architecture)
- Behavior Patterns Overview
- Command (Common interface, introduce first, common interface)
- Builder Pattern (Dapper, SQL query, Predicates)
- Chain of Responsibility (Complex processes, service call chain)
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
