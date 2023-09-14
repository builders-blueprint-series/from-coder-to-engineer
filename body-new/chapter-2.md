# Inheritance

## Key Terms

- Inheritance

- Virtual and Abstract

## Introduction

- Software engineering is not black and white, its a sea a gray
- Software is knowing what not to do
- Bad code comes from either not knowing, or not caring, you can't know everything, but you can eliminate the second
- Even though inheritance is a key pillar to object-oriented programming, we need to keep it to a minimum. The more inheritance we introduce in our system, the more issues we are inviting to happen.

## Goals

After completing this chapter will you will be able to...

Understand how adding to already implemented classes can cause issues.

Recognize that inheriting from multiple classes is unideal.

Realize that giving clients unimplemented interfaces will produce frustration.

## Wisdom

> The function of good software is to make the complex appear to be simple. (Booch, 2010)

- Complexity in software is an unavoidable force. We need to reduce it however we can. The proper use of inheritance and abstraction is one way of doing so. Inheritance allows reuse. Abstraction removes details. Both can work together and compliment each other.

## Simple Success - Costco

Costco Wholesale is a big-box retail store that was founded in 1994 just outside of Seattle. While Costco sells thousands of items in their store and online, each item they sale typically has very few options available to choose from. If you want to buy ketchup or napkins from Costco, you typically have one name brand and possible a generic brand to choose from. This is intentional on Costco's part because human behavior has a tendency to succumb to analysis paralysis. When people are presented too many options to choose from, they end up not choosing any of them because they are overwhelmed with options. For Costco, this means a loss of possible sales if people aren't buying products from them. Limiting options to customers ends up helping Costco in two ways; it forces customers to buy products, and it helps move product faster, which cuts down on costs.

## Code

### The Problem with Adding to Implementations (open/closed)

#### The Inability to Test X

> :large_blue_circle: We are using a switch expression which is a more compact type of switch statement. The logic is the same as a normal switch statement.

> :heavy_check_mark: Removing a constructor parameter has further encapsulated our class. Clients now have zero knowledge that our CustomerStatus enum exists.

- Breaking our Customer class into specific classes for each type of customer is an example of SRP. (Single Responsibility Principle) Each class is only responsible for dealing with a specific customer type.

> :warning: Don't fret about applying SRP everywhere. Like most things in software engineering, there's a place and time for everything.

#### Transforming X to Y

##### Step 1

##### Step 2

##### Step N

#### The Result of Y

#### The Ability to Test Y

### The Problem with Multiple Base Classes (liskov)

#### The Inability to Test X

:warning: Use abstract classes judiciously. Ideally you only touch an abstract class once. Having to update an abstract class is a potential sign refactoring is needed.

#### Transforming X to Y

##### Step 1

##### Step 2

##### Step N

#### The Result of Y

#### The Ability to Test Y

### The Problem with Unimplemented Interfaces (interface segregation)

#### The Inability to Test X

#### Transforming X to Y

##### Step 1

##### Step 2

##### Step N

#### The Result of Y

#### The Ability to Test Y

## Hard Truth

### Another form of wisdom

## Vocation

### Career Advice

## Automation

### Automate your processes

## Conclusion

### Simple paragraph that comes back to testing. Two sentences for summary, transition, go to next steps

:x: Multiple inheritance is not allowed in most modern language because it produces far more problems than it solves.

:warning: Inheritance is the second highest form of coupling after friendship. Stay vigilant against excessive inheritance as it _will_ cause problems.

:large_blue_circle: Software is about have the least amount of dependencies as possible. The fewer dependencies, the more our software can adapt and change at will.

:large*blue_circle: It's easy to confuse abstract and virtual. The key to remember is that virtual methods \_may* be overridden, while abstract methods _must_ be overridden.
