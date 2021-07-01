# Checklist

- [x] Introduction of chapter topic
- [x] Quote/s relating to the topic
- [x] Did you know?/Hints Light bulb information bits
- [x] Coding Horror Story
- [x] Lesson Learned Story
- [x] Best Practice Guidance
- [x] Every page has no more than 2/3 text
- [x] Career Hints
- [x] Real Life example relating to the topic
- [x] Lots of code
- [x] Conclusion

## Chapter 4: Principles to live by

### Introduction

- SOLID Principles
- Not taking on too much work
- Using concise interfaces

### Single Responsibility Principle

### Quote (do your job) - Bill Billicheck

Don't do too much

- Purpose of Single Responsibility
- SRP versus DRY (aren't mutually exclusive)
- God classes, bloated methods, misplaced logic, lots of conditional statements

### Career Advice

Small things add up

- Bad code has no single underlying issue
- Small issues cause large ones
- Fix your broken windows

### Open-Closed Principle and Liskov

Interfaces over inheritance

- Keep inheritance limited (1-2 max)
- Example of introducing an interface over base class
- Purpose of Open/Closed
- Purpose of Liskov
- Concrete, new, friends, internal

### Engineering Disasters

- Toyota Brake Controller

### Lesson Learned

:school: Making tradeoffs is normal, nothing is perfect. Software is pretty gray.

### Interface Segregation

Only provide what is needed

- Somewhat related to SRP
- Purpose of Interface Segregation

- Unimplemented interfaces, ignored methods

### Coding Horror Story

:scream: Frameworks for the sake of frameworks. (Abstracting over NServiceBus, 3 different DI frameworks)

### Dependency Inversion

Ideas over implementations

- Purpose of Dependency Inversion (relate back to polymorphism)
- DI vs DI (dependency injection) vs DI (inversion of control)
- Composition vs Aggregation
- Bad injection policies

### Food for Thought

:apple: Associate lines with cost

- Everyone line of code costs money (not absolute)
- Every line adds complexity

### Conclusion

- SRP can be pretty broad at times, use judgement
- Use an interface when possible
- Keep your interfaces streamlined
