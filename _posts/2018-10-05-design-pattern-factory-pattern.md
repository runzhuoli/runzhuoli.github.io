---
title: Design Pattern - Factory Pattern 
key: 20181005
tags: 
  - Design Pattern
  - Java
---
I am working on some simple AWS lambda functions now. To get the better performance, I decide to get rid of Spring framework in most of them as they are simple and straightforward. But without having a mature framework, it should be more carefully as I need to implement several methodologies by myself instead of using things provided by the framework. So, I decide to refresh my knowledge of the basics, like common design patterns in object oriented programming. I am going to talk about a few design patterns by summarizing the resources I found, putting my own thoughts and including some Java code examples in the next few posts. 

Today, I am going to start our journey with **Factory Pattern**.

<!--more-->

Factory pattern is a creational pattern that uses factory methods-either specified in an interface and implemented by child classes to deal with the problem of creating objects. The main goal is to take the object creation process out of the client code as the initialization process may be very complex or the code can not anticipate the concrete class when creating the object.

The common use cases are that the concrete classes are dependent on system environments, user input at runtime. For example, programs run in different databases cross stages or need to have some different logics based on users' location. In Spring, we can initialize a bean with different implementations based on the Spring active profile.

There are 3 types of factory pattern: simple factory, factory method and abstract factory. I cannot find a good answer of the differences between them. I am going to explain them based on my understanding.  

## Simple Factory

We can think simple factory is a simplify version of factory method pattern. It uses a single factory method to create objects from one family(a abstract class or interface). 

For example, we use a method to create different auto engines based on the brand type. 

![simple-factory-uml](https://s3.amazonaws.com/runzhuo-me/image/simple-factory.png)

```java
  public static Engine createEngine(Brand brand) {
    switch (brand) {
      case BMW:
        return new BmwEngine();
      case MERCEDES:
        return new MercedesEngine();
      default:
        throw new IllegalArgumentException();
    }
  }
```

## Factory Method

The problem with the above example is that when there is a new engine brand, we need to modify the existing code to add the new brand. It is against "Open for extension, but close to modification" principle. Indeed, simple factory is not an official design pattern from Design Patterns written by GOF. 

Factory method pattern solves this problem. It uses an interface which contains a factory method so we can have multiple implementations of factory method by implementing the factory interface. 

![factory-method-uml](https://s3.amazonaws.com/runzhuo-me/image/simple-factory.png)

Now, let's make the problem more complex. We assume that there are 100 engine brands in our store. We need a lot of concrete factories which can create different combinations of engines. Even, we want to build a factory at the run time. 

In order to achieve this, we can not define the concrete factory classes directly. We will use a map to identify what engines we can create in a factory and use the map to build the factory. It means the factory is depend on the map. Based on "Depend upon abstractions, not concretions" principle, we will use a builder to create the map dynamically so we decouple the factory and the concrete map. Let's implement it by using functional interface in Java 8.

```java
@FunctionalInterface
public interface Builder {
  void add(Brand brand, Supplier<Engine> supplier);
}

@FunctionalInterface
public interface EngineFactory {
  Engine create(Brand brand);

  static EngineFactory build(Consumer<Builder> mapBuilder) {
    Map<Brand, Supplier<Engine>> map = new HashMap<>();
    mapBuilder.accept(map::put);
    return brand -> map.getOrDefault(brand, () -> {
      throw new IllegalArgumentException();
    }).get();
  }
}

public class App {
  public static void main(String[] args) {
    EngineFactory factoryKit = EngineFactory.build(builder -> {
      builder.add(Brand.BMW, BmwEngine::new);
      builder.add(Brand.MERCEDES, MercedesEngine::new);
    });

    Engine engine = factoryKit.create(Brand.BMW);
  }
}
```

The build method in EngineFactory interface is called **higher order function**. Higher order function is a function that takes a function as an argument, or returns a function. In addition, we can use BiConsumer from Java directly instead of creating the Builder interface. 

## Abstract Factory

The abstract factory pattern provides a way to encapsulate a group of individual factories that have a common theme without specifying their concrete classes. It is a encapsulation of several factory methods which create relate products.

For example, we have a PartsFactory interface which response for creating auto parts and it implemented by 2 classes which only produce parts for a specific brand.

![abstract-factory-uml](https://s3.amazonaws.com/runzhuo-me/image/abstract-factory.png)

## Tips

1. What is a functional interface in Java?

    A functional interface has exactly one abstract method. Lambda expressions can be used to represent the instance of a functional interface.

2. How to create a bean when profile is not equal to 'B' in Spring?

    `<beans profile="A,!B">` or `@Profile("A", "!B")`
