# Java

## Inheritance

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

```
Map::getKeys, Map::size, etc.
```

## Streams

Helps iterate over collections and allow us to use functional programming. Introduced in Java 8.

Used internal iteration instead of external iteration like while or for loop.

Instead of doing all the operations for an iteration all at once, it will do the operation as a whole one at a time.

```
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


