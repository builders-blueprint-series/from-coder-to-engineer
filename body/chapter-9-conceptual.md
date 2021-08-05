# Checklist

- [x] Introduction of chapter topic
- [x] Quote/s relating to the topic
- [ ] Did you know?/Hints Light bulb information bits
- [x] Coding Horror Story
- [x] Lesson Learned Story
- [ ] Best Practice Guidance
- [x] Every page has no more than 2/3 text
- [x] Career Hints
- [x] Real Life example relating to the topic
- [x] Lots of code
- [x] Conclusion

## Chapter 9 Delegate Responsibility

### Introduction

- Responsibility deals with what classes are doing and what files are doing
- Extends to how code is distributed in the solution

### Too many conditional statements

- Minimize procedural code

### Lesson Learned

:school:

People dislike change. When presenting an idea to your team, go into the meeting aware that some may not reject it because they feel uncomfortable even if the idea is objectionably good.

### Classes doing too much

- God classes

### Coding Horror Story

:scream: "Bloated Repository"

### Best Practice

- Keep everything in a separate file, even simple interfaces

### Methods doing too much

- Embrace events (sending an email after singing up)

### Engineering Diaster Stories

Mars Climate Orbiter - keep everyone on the same page

### Food for Thought

:apple:

- Comments should describe why you choose to do something, not what the code is doing. (Exception if you're writing a 3rd party library)

### Logic in the wrong place

- Reverse hour glass code
- Skinny at the top and bottom
- Fat in the middle (application, domain)

### Career Advice

One line a day keeps the refactoring away

- Software is imperfect
- Software is never finished
- Software is entropic
- Fix one line a day (make this cultural)
- Turn a pr into a cultural impact

### Conclusion

- Code needs to be distributed evenly
- There is no hard rule for the length of anything, use your judgement
- Your code should mostly reside in your domain and application
