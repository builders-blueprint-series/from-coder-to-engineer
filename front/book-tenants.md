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

Frameworks for the sake of frameworks.

Writing something that already exists (ORM, motorola's stupid queue polling shit)

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

Making tradeoffs is normal, nothing is perfect. Software is pretty gray.

It may take two, three, or four iterations until you are finally happy with something. Don't be happy because "it works" (React search page)

Your customers will break things very quickly. Be flexible and open to change. Don't make assumptions because you believe something is ideal. (Fidel's POS refresh)

People dislike change. When presenting an idea to your team, go into the meeting aware that some may not reject it because they feel uncomfortable even if the idea is objectionably good.

Frameworks aren't friends

- Frameworks hide problems
- Frameworks are one more thing you need to know

## Career Advice

Bad software hurts developers

- Bad code is frustrating
- Bad code slows development
- Bad code hurts culture
- Bad software reinforces future bad software (cargo cult)

Bad software hurts customers

- Poor software is under performing
- Poor software is harder to interact with
- Customers aren't stupid (people can tell your app is bad)
(story moving from android to apple because i could tell the
ios apps were just red-headed stepchild)
(airline story us airways added wi-fi when people booked away)

Bad software costs money

- Bad code requires more developers
- Bad code requires more time
(Story about adding more ram because of crashes)
- Bad code increases turnover

Small things add up

- Bad code has no single underlying issue
- Small issues cause large ones
- Fix your broken windows

Easier to work with

- Simple is easy to change
- Simple is easy to add to
- Simple means more money saved, and more money that can be earned

Easier to understand

- Simple is easy to learn and memorize
- Simple is easy to onboard new team members
- How fast can you onboard a new team member?
- (Motorola can take weeks!)

The five minute rule

- Can you explain anything to a junior developer in five minutes
- Complex software doesn't mean complex code

One line a day keeps the refactoring away

- Software is imperfect
- Software is never finished
- Software is entropic
- Fix one line a day (make this cultural)
- Turn a pr into a cultural impact

The easier way to get a raise or promotion is to save your company money
Nothing will make your boss/CEO happier than telling him you saved him money
Saving money comes in a lot of ways

## Real Life Examples

Costco - Chapter 3
In-N-Out - Chapter 5
Allegiant Airlines - Chapter 7
Chick-Fil-A - Chapter 9

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

Design patterns were inspired from architecture, "A Pattern Language"

## Food for thought

- Simplicity is about removing options

Associate lines with cost

- Everyone line of code costs money (not absolute)
- Every line adds complexity

- Seatbelt/helmet analogy for why testing is possible

- Naming, find a common way of doing things a stick with it.

- Better to be consistent and not perfect than inconsistency perfect

- Comments should describe why you choose to do something, not what the code is doing. (Exception if you're writing a 3rd party library)
