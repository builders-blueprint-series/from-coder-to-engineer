# Chapter 9

## Patterns To Remember

After completing this chapter, you will be able to

- Understand the purpose of design patterns

- Recognize that all patterns have a time and place

- Be able to recognize when to use design patterns in your applications

- Learn how patterns can be used together to create well engineered software

- Find how to find a common interface between software components

- Implement specific patterns into your software

## Introduction

1. Design patterns are commonly associated with the GOF book published in the early 1990's

2. Other patterns exist in software besides the commonly published GOF patterns

3. Patterns are useful because they are tried and true pieces of software engineering that have stood for decades

4. Understanding where and when to use a pattern is the hardest and most important part

5. This book tried to focus on behavior patterns over others

## The Purpose of Design Patterns

1. Design patterns are there to make your software life easy

2. All patterns have a time and place where they are appropriate

3. It is your job as a software engineering to learn and recognize when to use a pattern

4. When used properly design patterns are powerful tools that turn good software into great software

### There are no such things as anti-patterns

A narrative that is echoed by some people in the software industry is that certain patterns out there are "anti-patterns". In that they hold a net negative effect on ones software. Many people imply that these "anti-patterns" should never be used under any situation. Many times I have heard a co-worker at work blurt out the phrase, "Singleton is an anti-pattern." Or, "Service Locator is an anti-pattern." It is unfortunate that such an opinion exists in our industry.

I would like to present a different opinion. There is no such thing as an anti-pattern. Only an improperly applied one.

For example, if your application uses a Http client, WCF channel factories, or gRPC channel. These are typically registered as singletons in a dependency injection framework. Just because you are not writing your own singleton class for these objects does not mean you are not using the pattern. Your dependency injection framework or IOC containing is using the pattern from which you get to benefit from.

As for Service Locators, every dependency injection framework or IOC container is also using a service locator, or relying on a service locator to resolve objects when they are needed. I have yet to see a blog article argue against dependency injection, so it would be rather hypocritical to argue against service locators, but support an IOC container.

Every pattern have a time and place when it can and SHOULD be applied. For some patterns, this window of opportunity is very small, for others it is abundant. Remember, as the curator of your application, only you know what is truly appropriate. Just because certain pattern may be a good or bad fit for someone elses application does not mean that it is equally good or bad for yours as well.

This notion goes beyond software anti-patterns, do an internet search for the term "anti-pattern" that you will come back with blog article after blog article of "Product Owner Anti-Patterns", "Scrum Anti-Patterns", and "Dev Ops Anti-Patterns". Some people event assert the notion that design pattern in and of themselves are an anti-pattern. Again, people do not take this absolutism by others as a software dogma that be be adhered to at every possible opportunity. All software applications are different, and each needs to satisfy different requirements.
