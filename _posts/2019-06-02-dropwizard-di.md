---
title: HK2 Dependency Injection
long_title: Dropwizard Dependency Injection 💉
layout: blog
categories: 
    - dropwizard 
    - java 
    - hk2 
    - dependency injection 
    - inversion of control
---

Dropwizard has been around a while now. Looking through the docs gives the impression that you need to use a third party module to handle dependency injection. According to the docs you manually wire up the dependency tree. Dropwizard has an hidden ace up its sleeve though, it has a dependency injector built in!! By using Jersey, it gives us access to the HK2 injector. The docs for this can be a bit cryptic. Combine this with Dropwizards lack of docs on the issue and it can be a bit hard to see how everything hangs together. So lets dive in and take a look.

In a dropwizard application, where HK2 manages the lifecycle of the app, you organise your application around modules. These are groups of cooperating classes. You can extend this to your use case (ie. feature modules, tiered modules, whatever takes your fancy). It may even be that you dont really want to break your app into seperate modules, you just want to have things wired up without having to manually resolve dependency order; thats ok too just put them all in one module. It will all depend on scale, if its a majestic monolith or a microservice.

## A Little Bit of Background on Dependency Injection and Inversion of Control

The meaning of many terms in programming become muddled through the years. The original meanings have become synonomous with libraries that do a particular thing and as they shift and change so too does the original meaning. Dependency injection has suffered quite badly from this.

> "I don't know what you mean by 'glory,' " Alice said. 
> Humpty Dumpty smiled contemptuously. "Of course you don't—till I tell you. I meant 'there's a nice knock-down argument for you!' "
> "But 'glory' doesn't mean 'a nice knock-down argument'," Alice objected.
> "When I use a word," Humpty Dumpty said, in rather a scornful tone, "it means just what I choose it to mean—neither more nor less."
> "The question is," said Alice, "whether you can make words mean so many different things."
> "The question is," said Humpty Dumpty, "which is to be master—that's all."
> Alice was too much puzzled to say anything, so after a minute Humpty Dumpty began again. "They've a temper, some of them—particularly verbs, they're the proudest — adjectives you can do anything with, but not verbs—however, I can manage the whole lot! Impenetrability! That's what I say!"

// insert a concise and reasonably simple definition of dependency injection

So what do we consider dependency injection. Well, it's a design pattern in software development where the dependencies of an object (or function) are provided to it from outside rather than created internally, allowing for greater flexibility, modularity, and testability. In the context of our Java application that means no use of the `new` keyword inside our classes.

But HK2 doesnt actually do dependency injection (we do that ourselves). What it actually does is inversion of control. Inversion of control is a design principle in software development where the control of the flow of a program is inverted, meaning that the responsibility for managing and coordinating dependencies is moved from the application code to a framework or container, allowing for more flexibility and easier maintenance. By inverted we mean that wiring up is done from the top down (by the container in our `main`) instead of from the bottom up (in the classes themselves).

So dependency injection is a pattern that allows for the implementation of inversion of control by providing a way for an object or function to receive its dependencies from an external source. Dependency injection and inversion of control are closely related and often used together to create modular and flexible software systems.


## The HK2 Injection Container

Jersey is used in Dropwizard as the primary framework for building RESTful web services. It provides a simple and consistent API for building HTTP endpoints, handling requests and responses, and serializing and deserializing data. Jersey also includes support for features such as resource methods, filters, and exception handling, which makes it easy to build robust and scalable web services.

HK2 is used in Dropwizard as the default dependency injection framework. It provides a lightweight and flexible way to manage the lifecycle of objects and to inject dependencies into those objects. HK2 allows you to define services, resources, and other components as simple Java classes, and then use annotations to tell HK2 how to create and manage those objects. 


Adding the 

```java
bind(ContentModuleManager.class)
    .to(ContentModuleManager.class)
    .in(Singleton.class);
```

```java
private Service service;

@Inject
public OtherService(Service service) {
   this.service = service;
}
```

```java
@RequiredArgsConstructor(onConstructor = @__(@Inject))
````