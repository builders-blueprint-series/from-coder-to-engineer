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

## Chapter 7 Crush Coupling

### Introduction

- Code should be a serial dater
- Don't marry anything you don't have to
- Coupling makes testing harder
- Coupling inhibits change later

### Quote (don't marry anything you can date)

### The "new" keyword

- "new" is glue, no "new" is a weld

### Lesson Learned

:school: - Factories probably don't need an interface

### Concrete dependencies

- Don't marry concrete types
- Concrete types destroy testing
- Concrete types inhibit polymorphism

### Coding Horror Story

:scream: Brian's GroovyScript story

### Friend classes

- Friend classes are the highest form of coupling
- (Internal in other languages)

### Career Advice

- The easiest way to get a raise or promotion is to save your company money
- Nothing will make your boss/CEO happier than telling him you saved him money
- Saving money comes in a lot of ways

### Inheritance

- Inheritance is the second highest form of coupling
- O and L in SOLID are because of inheritance

### Engineering Disaster Stories

MIM-104 Patriot Missile Launcher

### Dependency Injection

- Relying on dispose  
- Injecting the container  
- Resolving from the container  
- Over injecting (5+ things in a class)
- Fancy injecting  (stupid shit you shouldn't do)
- Inappropriate registration
- Dependency Swallowing (singleton over a instance)

#### Best Practice

- Use the Microsoft base container
(It has no fancy options, which is a good thing!)
- DI should be seen, but not heard
- Instance, Scoped, Singleton

#### Food For Thought

:apple: Reflection "with great power comes great responsibility"
(With with reflection to auto-register stuff)

### Did you Know?

:thinking: SRP vs DRY - not mutually exclusive

### Conclusion

- Coupling inhibits testing, change, and quick feedback
- Code should not take any dependencies it doesn't have to
