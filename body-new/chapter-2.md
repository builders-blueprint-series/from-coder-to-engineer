# Inheritance

## Key Terms

- Inheritance

- Virtual and Abstract

## Introduction

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

### Headliner - Costco breaths simplicity.

#### 2nd Idea of title (topic sentence) - Costco gives you very few options.

##### Supporting Evidence - Carry one name and one generic brand. Grocery store carries everything.

#### 2nd Mirror - This is to get you to remove decisions and get you through the store as fast as possible.

### Bottom-liner - Costco is the definition of simplicity.

## Code

### The Problem with Adding to Implementations

#### The Inability to Test X

#### Transforming X to Y

##### Step 1

##### Step 2

##### Step N

#### The Result of Y

#### The Ability to Test Y

### The Problem with Multiple Base Classes

#### The Inability to Test X

#### Transforming X to Y

##### Step 1

##### Step 2

##### Step N

#### The Result of Y

#### The Ability to Test Y

### The Problem with Unimplemented Interfaces

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

---
:x: Multiple inheritance is not allowed in most modern language because it produces far more problems than it solves.

---

---
:warning: Inheritance is the second highest form of coupling after friendship. Stay vigilant against excessive inheritance as it _will_ cause problems.

---

---
:large_blue_circle: Software is about have the least amount of dependencies as possible. The fewer dependencies, the more our software can adapt and change at will.

---
