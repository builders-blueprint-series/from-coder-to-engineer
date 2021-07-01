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

## Chapter 7 Embrace Encapsulation

### Introduction

- See how bad encapsulation inhibits testings
- Bad encapsulation forces more unwanted tests

### Quote (Don't expose what do you don't want exposed)

### Public setters

- Root of all evil
- Embrace Constructors
- Embrace static constructor methods for clarity

### Career Advice

Easier to work with

- Simple is easy to change
- Simple is easy to add to
- Simple means more money saved, and more money that can be earned

### Excessive Properties

- Encapsulate object details
- Abstract primitive types (Name class vs 2 strings)

### Real Life Simplicity

Allegiant Airlines

### Private methods

- Second worse thing to public setters
- Push private methods down

### Did you know?

:thinking: Inheritance vs Encapsulation - not mutually exclusive

### Extension methods

- Purpose of extension methods
- Visible to all types
- Can't mock extension methods

### Lesson Learned

:school:

- You will struggle with where and how to apply static methods
- Very much a learning process

### Food for Thought

:apple: Simplicity is about removing options

### Exposed collections

- Collections are arbitration
- Collections are naked

### Coding Horror Stories

:scream: ElasticSearch library that was beyond comprehendible, that just copy the same search, "I wrote this for job security"

### Leaky domain

- Clients don't need everything
- Embrace diverse responses
- Response objects allow for 1-M with clients

### Lesson Learned Stories

:school: Frameworks aren't friends

- Frameworks hide problems
- Frameworks are one more thing you need to know
- You are still responsible for that code

### Entity Framework

- Encapsulation classes
- Embrace owned types
- Encapsulate collections

### Conclusion

- Eliminate public setters and private methods when possible
- Push logic down to where it belongs
- Don't expose the domain or collections
- Encapsulate EF where possible
