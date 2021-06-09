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

- [ ] Introduction of chapter topic
- [ ] Quote/s relating to the topic
- [ ] Did you know?/Hints Light bulb information bits
- [ ] Coding Horror Story
- [ ] Lesson Learned Story
- [ ] Best Practice Guidance
- [ ] Every page has no more than 2/3 text
- [ ] Career Hints
- [ ] Real Life example relating to the topic
- [ ] Lots of code
- [ ] Conclusion

## Markdown Icons

Info - :information_source:

Note - :memo:

Warning - :warning:

Hint - :bulb:

Correct - :heavy_check_mark:

Incorrect - :x:

Careful - :eyes:

Quote - :speech_balloon:

Horror Story - :scream:

Food for Thought - :apple:

Did you Know? - :thinking:

Lesson Learned - :school:

## Coding Horror Stories

ElasticSearch library that was beyond comprehendible, that just copy the same search, "I wrote this for job security"

Three Different DI libraries in the same solution - Chapter 9, Repetition

"Bloated Repository" - Chapter 10, Responsibility

Brian's GroovyScript story

## Lesson Learned Stories

Factories probably don't need an interface - Chapter 8, Coupling

Copy and Pasting code is a dead-give away for possible change - Chapter 9, Repetition



Your customers will break things very quickly. Be flexible and open to change. Don't make assumptions because you believe something is ideal. (Fidel's POS refresh)

People dislike change. When presenting an idea to your team, go into the meeting aware that some may not reject it because they feel uncomfortable even if the idea is objectionably good.

Frameworks aren't friends

- Frameworks hide problems
- Frameworks are one more thing you need to know

## Career Advice

Easier to work with

- Simple is easy to change
- Simple is easy to add to
- Simple means more money saved, and more money that can be earned

Easier to understand

- Simple is easy to learn and memorize
- Simple is easy to onboard new team members
- How fast can you onboard a new team member?
- (Motorola can take weeks!)

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

Allegiant Airlines - Chapter 7
Chick-Fil-A - Chapter 9

MIM-104 Patriot Missile Launcher - Chapter 8
Mars Climate Orbiter - Chapter 10

## Did you know?

Inheritance vs Encapsulation - not mutually exclusive, Chapter 7 Encapsulation

SRP vs DRY - not mutually exclusive, Chapter 8, Coupling

You are responsible for all code in your solution, even if you didn't write it. - Chapter 10, Responsibility

Common Interfaces are all around us (USB, Electrical sockets, driving a car) - Chapter 5, Patterns

Code coverage is not a good indicator of anything.

## Food for thought

- Simplicity is about removing options

- Seatbelt/helmet analogy for why testing is possible

- Naming, find a common way of doing things a stick with it.

- Comments should describe why you choose to do something, not what the code is doing. (Exception if you're writing a 3rd party library)

- Reflection "with great power comes great responsibility"
(With with reflection to auto-register stuff)
