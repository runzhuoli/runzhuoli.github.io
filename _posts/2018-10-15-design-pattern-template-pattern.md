---
title: Design Pattern - Template Method Pattern & Strategy Pattern 
key: 20181015
tags: 
  - Design Pattern
  - Java
---

Today I am going to talk about **template method pattern** and **strategy pattern**. In addition, we will discuss how to leverage them with functional interface in Java 8. These two design patterns are both belong to behavioral patterns in Design Patterns written by GOF.

<!--more-->

Let's start with template method pattern. Based on the definition in Design Patterns:

Template method pattern is:

> Define the skeleton of an algorithm in an operation, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.

Consider we are building an application for a pizza store and we need a method to cook a pizza. There are three steps to cook a pizza in the pizza store: 

1. Prepare pizza dough; 
2. Add sauce and toppings; 
3. Bake the pizza. 

The first step is a common behavior and the second and third steps are various for different pizza types. We can solve this problem by using template method pattern as below:

![template-method-uml](https://s3.amazonaws.com/runzhuo-me/image/template-method.png)

Now let's assume we have a lot of new types of pizza in store, so we need many new pizza cookers(subclasses). However, some of the cookers have the same logic in step 2 and some of them have the same logic in step 3. We want these cookers can reuse the same logic through application to reduce code duplication. Strategy method is a good way to deal with this problem.

Strategy pattern is:

> Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

Strategy pattern is implemented through **object composition**. A context class is configured with concrete strategy objects and maintains the references to strategy objects. We can design the structure as below:

![strategy-pattern-um](https://s3.amazonaws.com/runzhuo-me/image/strategy-pattern.png)

With the availability of functional interface in Java 8 we could use **function composition** instead of object composition to solve this problem with a single functional interface. 

```java
@FunctionalInterface
public interface PizzaCooker {
  void cookPizza(Pizza pizza);

  default PizzaCooker setup(Consumer<Pizza> addToppings, Consumer<Pizza> baking) {
    return pizza -> {
      // prepare dough logic
      System.out.println("making pizza dough.");
      addToppings.accept(pizza);
      baking.accept(pizza);
    };
  }
}
```

**Function composition** is the process of combining two or more functions to produce a new function. Composing functions together is like snapping together a series of pipes for our data to flow through. It provides more flexibility to control the concrete logic in the code. We do not need to create multiple subclasses for different implements, instead just declare different functions. 
