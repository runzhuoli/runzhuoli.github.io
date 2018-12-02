---
title: Executors, a short-path or an evil? 
key: 20181123
mermaid: true
tags: 
  - Java
  - Multithreading
---

When google "java thread pool", we could find a lot of examples and resources to follow. However, most of them create thread pools by using methods from java.util.concurrent.Executors. This is an easy way to initiate a thread pool but we should avoid this approach in real projects, specially there is high concurrency load in the application.
 
<!--more-->

## Thread Pool

### Parallelism vs Concurrency

In almost all modern OS's, system schedule executions by thread. In most cases, our hardware contains more than one logical cores, which guarantee multi threads can be executed at the same time. Usually, we describe multi threads are running parallel or concurrently. However there are some differences them.

For parallelism, it means multiple threads are running at the same time. 

CPU 1: A - - - - - - - ->

CPU 2: B - - - - - - - ->

For concurrency, it means the execution of multiple threads are overlaps. They may be running in the same way as parallelism or being interleaved on the processor as below:

CPU 1: A - -> B - -> A - -> B - ->

Java threads are running concurrently and Java does not manage the schedule of threads, which is hand over to the operation system. It means there is no guarantee that multiple threads of a Java process are running parallel at the running time.

### Thread Pool in Java

By refering a great explanation of java thread pool from [Introduction to Thread Pools in Java](https://www.baeldung.com/thread-pool-java-and-guava)

> When you use a thread pool, you write your concurrent code in the form of parallel tasks and submit them for execution to an instance of a thread pool. This instance controls several re-used threads for executing these tasks.
>
> The pattern allows you to control the number of threads the application is creating, their lifecycle, as well as to schedule tasks’ execution and keep incoming tasks in a queue.

![thread-pool](https://www.baeldung.com/wp-content/uploads/2016/08/2016-08-10_10-16-52-1024x572.png)

As we can see, all tasks submitted to the executor service are queued in a blocking queue and these tasks are waiting for the next available thread in the pool to pick up for execution. Obviously, if there is no limitation of the size of waiting tasks, the application may goes into a out of resources(memory) issue.

## Executors

This suggestion comes from [Alibab Java Coding Guideline](https://alibaba.github.io/Alibaba-Java-Coding-Guidelines/#orm-rules), and I strongly recommend every java developer could look through this guideline. 

In the concurrency section, there is the rule:

> **[Mandatory]** A thread pool should be created by ThreadPoolExecutor rather than Executors. These would make the parameters of the thread pool understandable. It would also reduce the risk of running out of system resource.
>
> Note:
>
> Below are the problems created by usage of Executors for thread pool creation: 
>
>1) `FixedThreadPool` and `SingleThreadPool`:
>
> Maximum request queue size `Integer.MAX_VALUE`. A large number of requests might cause OOM. 
>
>2) `CachedThreadPool` and `ScheduledThreadPool`:
>
> The number of threads which are allowed to be created is `Integer.MAX_VALUE`. Creating too many threads might lead to OOM.

### Single Thread Pool & Fixed Thread Pool

Let's look into the code of Excutors. `newSingleThreadExecutor` and `newnewFixedThreadPool` methods initiate a new `ThreadPoolExecutor` by passing a `new LinkedBlockingQueue<Runnable>()` in the parameters. However, the default constructor of LinkedBlockingQueue creates a queue with a capacity of `Integer.MAX_VAULE` as below, which means there is no limitation of the amount of tasks submitted into the queue and it will cause an out of memory issue when queued task keep increasing.

```java
/*
 * Creates a {@code LinkedBlockingQueue} with a capacity of
 * {@link Integer#MAX_VALUE}.
 */
public LinkedBlockingQueue() {
  this(Integer.MAX_VALUE);
}
```
### Cached Thread Pool & Scheduled Thread Pool

When we look into `newCachedThreadPool` and `newScheduledThreadPool` methods we will find the samiliar problem. The max pool size of thread pool executor is set to `Ingeger.MAX_VAULE`, which means we are allowed to create as many threads as we want.

```java
new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>(),
                                      threadFactory);
```

In summary, we should use **ThreadPoolExecutor** to create a thread pool instead of Executors. By using ThreadPoolExecutor, we can define the core size, max pool size, waiting task queue size and a thread factory, which can help us to manage thread names in the pool, of the new thread pool. Executors covered too many details and remove the limitation of task queue size or max pool size in a hidden way.
