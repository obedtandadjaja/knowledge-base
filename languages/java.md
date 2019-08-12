# Java

## Inheritance

Java only allows us to extend 1 class, but there is no limit on how many classes you can implement.

We can pass child classes to methods requiring parent classes since child classes are forced to implement parent class methods.

```java
public interface ParentClass {
  public void method1();
  public void method2();
}

public class ChildClass implements ParentClass {
  public void method1() {
    // implementation
  }
  public void method2() {
    // implementation
  }
}

public class Main {
  public static void main(String[] args) {
    ParentClass pc = new ParentClass();
    ChildClass cc = new ChildClass();
    doSomething(pc);
    doSomething(cc);
  }
  
  public static void doSomething(ParentClass obj) {
    obj.method1();
    obj.method2();
  }
}
```

Inheritance wildcard - doesn't care the type as long as it extends a base class

```java
public class AnotherClass<T extends ParentClass> {}

public static void main(String[] args) {
  AnotherClass<ParentClass> ac = new AnotherClass<>();
  AnotherClass<ChildClass> ac2 = new AnotherClass<>();
}
```

## Data Structure

![picture](http://www.sergiy.ca/img/doc/java-map-collection-cheat-sheet.gif)

Iterable -> Collection -> (Set, List, Queue)

Map is not in collections and thus it does not implement iterable.

## Functional interfaces

How to pass around code

```java
@FunctionalInterface
public interface GreetingMessage {
  public abstract void greet(String name);
}

public static void main(String[] args) {
  GreetingMessage gm = new GreetingMessage() {
    @Override
    public void greet(String name) {
      System.out.println("Hello " + name);
    }
  }
  gm.greet("Obed");
  
  // shorter version - lambda
  GreetingMessage gm2 = (name) -> {
    System.out.println("Hello " + name);
  }
  gm2.greet("Obed");
}
```

## Method Reference

Make lambdas shorter if method called does not have any parameters

```java
Map::getKeys, Map::size, etc.
```

## Streams

Helps iterate over collections and allow us to use functional programming. Introduced in Java 8.

Used internal iteration instead of external iteration like while or for loop.

Instead of doing all the operations for an iteration all at once, it will do the operation as a whole one at a time.

```java
books.stream()
  .map(Book::decompress)
  .filter(book -> book.getAuthor().startsWith("J"))
  .forEach(System.out::println);
```

You can run iterations in parallel this way. Using multiple cores to do this task in parallel.

```
books.parallelStream()
  .map(Book::decompress)
  .filter(book -> book.getAuthor().startsWith("J"))
  .forEach(System.out::println);
```

If running on small data set it is sometimes faster to do it in one stream. Parallel executions are possible with functional programming because we are doing one task for the entire data set before moving on to the next task.

## Modularity

In Java we can create modules to decouple subsets of the system to different repositories. Every module has a `module-info.java` file which tells what dependencies the module need and what classes it exposes to public.

```java
module HelloWorld {
  requires java.util;
  exports helloworld; // name of the module
}
```

Structure: Application -> Module -> Package -> Class

## Threads

When to use threads?
1. Blocking I/O - need other threads to run in the background
2. Independent tasks

2 ways of using threads in java

1. Use class that extends Thread class

```java
public ThreadExample extends Thread {
  @Override
  public void run() {
    int i = 1;
    while (i <= 100) {
      System.out.println(i + " " + this.getName());
      i++;
    }
  }
}

public static void main(String[] args) {
  ThreadExample thread1 = new ThreadExample();
  thread1.setName("thread1");
  System.out.println(thread1.isAlive()); // returns false
  
  thread1.start();
  System.out.println(thread1.isAlive()); // returns true
}
```

2. With Runnable interface - benefit is you can extend other classes at the same time

```java
public RunnableExample extends Runnable {
  @Override
  public void run() {
    int i = 1;
    while (i <= 100) {
      System.out.println(i + " " + Thread.currentThread.getName());
      i++;
    }
  }
}

public static void main(String[] args) {
  Thread thread1 = new Thread(new RunnableExample());
  thread1.setName("thread1");
  thread1.start();
  
  // Runnable is a functional interface
  Thread thread2 = new Thread(new Runnable() {
    @Override
    public void run() {
      int i = 1;
      while (i <= 100) {
        System.out.println(i + " " + Thread.currentThread.getName());
        i++;
      }
    }
  });
  thread2.setName("thread2");
  thread2.start();
  
  // Shorten further with lambda
  Thread thread3 = new Thread(() -> {
    int i = 1;
    while (i <= 100) {
      System.out.println(i + " " + Thread.currentThread.getName());
      i++;
    }
  });
  thread3.setName("thread3");
  thread3.start();
}
```

## Synchronized Method

Only one thread can enter the method at a time

```java
static synchronized void withdraw(BankAccount account, int amount) {
  // implementation
}
```

## Synchronized Block

Takes a monitor object (whatever you put as the argument to the synchronized block), and restricts any other thread to access the monitor object.

```java
new Thread(() -> {
  synchronized(obj) {
    synchronized(obj2) {
      // do something
    }
  }
});
```

## Try With Resources

Auto close closeable resources

```java
try(
  BufferedReader reader = new BufferedReader(new StringReader("Hello World"));
  StringWriter writer = new StringWriter();
) {
  writer.write(reader.readLine());
} catch (IOException ioe) {
}
```
