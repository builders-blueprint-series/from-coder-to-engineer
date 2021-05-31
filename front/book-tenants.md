# Book Tenants

1. Writing and code should be as simple as possible

2. Keep words to code ratio around ~55/45

3. Purpose of examples is to show why not how

4. Every example should follow the format of:

   - Presenting a piece of code

   - Explaining why the code is sub-optimal due to testing issues

   - Present an alternative

   - Show how testing has improved afterwards

5. Every example should come back to the theme of testing as a benchmark, shortening the feedback loop, and enforcing simplicity via common interfaces

## Chapter Format

Introduction of chapter topic
Quote/s relating to the topic
Did you know?/Hints Light bulb information bits
Coding Horror Story
Lesson Learned Story
Best Practice Guidance
Every page has no more than 2/3 text
Career Hints
Real Life example relating to the topic
Lots of code
Conclusion

## Markdown Icons

Info - :information_source:

Note - :memo:

Warning - :warning:

Hint - :bulb:

Correct - :heavy_check_mark:

Incorrect - :x:

Careful - :eyes:

## Coding Horror Stories

"I've had great success without unit testing". - Chapter 6, Tests
"We'll catch it in code review". - Chapter 2, Compiler
Frameworks for the sake of frameworks
Writing something that already exists (ORM, motorola's stupid queue polling shit)
People love to over-engineer almost everything
ElasticSearch library that was beyond comprehendible, that just copy the same search, "I wrote this for job security"
Three Different DI libraries in the same solution - Chapter 9, Repetition
"Bloated Repository" - Chapter 10, Responsibility
"Have to add more RAM because the server keeps crashing" - Guy at bar
Brian's GroovyScript story

## Lesson Learned Stories

XML Creation requiring Approvals testing - Chapter 6, Testing
Factories probably don't need an interface - Chapter 8, Coupling
Understanding polymorphism was the key for understanding design patterns - Chapter 5, Patters
Copy and Pasting code is a dead-give away for possible change - Chapter 9, Repetition
Seeing patterns everywhere, you can overuse them.
Making tradeoffs is normal, nothing is perfect.

## Career Advice

Code and coders are cheap, engineers are rare and valuable - Chapter 1

Speak up now, or pay later
Don't be timid about called out bad software
Precedence doesn't mean correctness
Poor software proliferates exponentially

Bad software hurts developers
Bad code is frustrating, slows development, hurts culture, and reinforces future bad software

Bad software hurts customers
Under-performing, hard to interact with, customers aren't stupid
(ios vs android, us airways wi-fi booking)

Bad software costs money
Requires more developers, more time, increases turnover

Small things add up quickly
Bad code has no single underlying issue
Small issues cause large ones
Fix your broken windows

The Five Minute Rule
Can you explain something to a junior developer in five minutes
Complex software doesn't mean complex code

One line a day keeps the refactoring away
Software is imperfect, never finished, entropic, turn a line a day into a cultural impact

The easier way to get a raise or promotion is to save your company money
Nothing will make your boss/CEO happier than telling him you saved him money
Saving money comes in a lot of ways

Anything that you allow to happen, will happen
Every line of code sets a standard
Cargo cult programming is a real thing

## Real Life Examples

Southwest Airlines - Chapter 1
Costco - Chapter 3
In-N-Out - Chapter 5
Allegiant Airlines - Chapter 7
Chick-Fil-A - Chapter 9

Tacoma Narrows Bridge - Chapter 2
Toyota Brake Controller - Chapter 4
737 MAX - Chapter 6
MIM-104 Patriot Missile Launcher - Chapter 8
Mars Climate Orbiter - Chapter 10

## Did you know?

Asynchronous, what goes on with async, its a state machine! - Chapter 5, Patterns

Software is a very immature process, we haven't been doing it for very long

Software is not a physical science, it's based in math, but does not apply it

Inheritance vs Encapsulation - not mutually exclusive, Chapter 7 Encapsulation

SRP vs DRY - not mutually exclusive, Chapter 8, Coupling

You are responsible for all code in your solution, even if you didn't write it. - Chapter 10, Responsibility

Common Interfaces are all around us (USB, Electrical sockets, driving a car) - Chapter 5, Patterns

Code coverage is not a good indicator of anything.

It's your application, only you know what is truly the best solution. What works for others may be wrong for you, and visa-versa.

