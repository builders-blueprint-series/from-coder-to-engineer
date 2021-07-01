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

## Chapter 9 Repudiate Repetition

### Introduction

- Don't do the same thing over and over again
- Don't copy/paste code, find a solution
- Repetition forces more testing

### Quote (repetition)

:speech_balloon:

### Duplication in the presentation layer

- Controllers have one purpose
- Its the same process every time (validate, catch, log, return)
- Consolidate the process

### Coding Horror Story

:scream: Three Different DI libraries in the same solution

### Duplication in the data layer

- Data classes have one purpose
- Its the same process every time (all, one, add, update, remove)
- Consolidate the process

### Lesson Learned

:school: Copy and Pasting code is a dead-give away for possible change

### Checks everywhere

(functional opportunity)

- If/else/switch hell
- Fail early and throw

### Career Advice

Easier to understand

- Simple is easy to learn and memorize
- Simple is easy to onboard new team members
- How fast can you onboard a new team member?
- (Motorola can take weeks!)

### Food for thought

:apple:

- Naming, find a common way of doing things a stick with it.
- Be descriptive if you have to

### Logging everything

- Logging doesn't replace testing
- Logging is line noise

### Real Life Simplicity

Chick Fil A - The simplicity of six days a week

### Conclusion

- Duplication wastes time, money, and energy on every developer
- Remove duplication though finding a common interface
- Duplication forces you to write more tests
