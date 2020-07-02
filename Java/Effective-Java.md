# Effective Java
This is a summary of the book "Effective Java, Second Edition" by Joshua Bloch.

It will have each item in the book, summarized as well as possible.

## Index
These are all the items present in the summary(chapter 1 is an introduction):
 * [Chapter 2 - Creating and Destroying Objects](#Chapter-2---Creating-and-Destroying-Objects)
   - [Item 1 - Consider static factory methods instead of constructors](#Item-1---Consider-static-factory-methods-instead-of-constructors)
   - [Item 2 - Consider a builder when faced with many constructor parameters](#Item-2---Consider-a-builder-when-faced-with-many-constructor-parameters)
   - [Item 3 - Enforce the singleton property with a private constructor or an enum type](#Item-3---Enforce-the-singleton-property-with-a-private-constructor-or-an-enum-type)
   - [Item 4 - Enforce noninstantiability with a private constructor](#Item-4---Enforce-noninstantiability-with-a-private-constructor)
   - [Item 5 - Avoid creating unnecessary objects](#Item-5---Avoid-creating-unnecessary-objects)
   - [Item 6 - Eliminate obsolete object references](#Item-6---Eliminate-obsolete-object-references)
   - [Item 7 - Avoid finalizers](#Item-7---Avoid-finalizers)

# Chapter 2 - Creating and Destroying Objects

## Item 1 - Consider static factory methods instead of constructors

**Advantage 1** -> Static factory methods give the programmer the advantage of naming the factory methods in order to obtain clearer code, avoid having to look into the documentation.

**Advantage 2** -> Static factory methods avoid having to create an instance of the object each time they are called, they give the programmer the ability to user preconstructed instances in inmutable clases or cache previous instances. It can greatly improve performance if objects are large and are frequently requested.

**Advantage 3** -> Static factory methods can return any subtype of the class, gives more flexibility.

**Advantage 4** -> Static factory methods reduce the verbosity of creating parameterized type instances.

**Disadvantage 1** -> Providing only static factory methods makes classes without public/protected methods not able to be subclassed.

**Disadvantage 2** -> Static factory methods not readily distinguishable from other static methods, cannot determine if it is a new instance easily. Some great common names are `valueOf`, `of`, `getInstance`, `newInstance`, `getType` and `newType`.

## Item 2 - Consider a builder when faced with many constructor parameters

Static factories and constructors do not scale well with optional parameters.

The *telescoping constructor* is not scalable -> It creates a constructor with the required parameters and many with the different optional parameters.

The *JavaBeans pattern* allows for inconsistencies and requires for the programmet to take into account syncronization issues.

The solution is the **Builder Pattern**, the client calls a constructor (or static factory) with all of the required parameters and gets a builder object. Then the client calls setter-like methods on the builder object to set each optional parameter of interest. Finally, the client calls a parame- terless build method to generate the object, which is immutable.

The structure is the following:
```
public class MyClass {
    <parameters, required + optional>
    ...
    public static class Builder{
        <parameters, required + optional>
        ...
        <constructor with required parameters>
        ...
        <setters for optional parameters that return this>
        ...
        public MyClass build(){
            ...
        }
    }
    ...
    private MyClass(Builder b){
        this.property = b.property;
        ...
    }
}
```

If the status of the given required variables is not correct, the `build()` method should throw `IllegalStateException`.

The behaviour of the builder can be expressed with this interface:
```
// A builder for objects of type T
public interface Builder<T> {
    public T build();
}
```

The method `newInstance` from the `Class` object has a lot of issues, so it shouldn't be used. It may attempt to invoke a constructor that may not exist, it propagates exceptions thrown by the constructor used and the client code has to be mindful of `InstantiationException` and `IllegalAccessException` at runtime. It breaks compile-time exception checking, using builder corrects this mistakes.

## Item 3 - Enforce the singleton property with a private constructor or an enum type

A *Singleton* is a class that can be instanced only once.

There are 2 ways in which to implement the Singleton pattern, both based on private constructors and exporting a public static member in order to provide access to the instance. No public constructor guarantees that, if the pattern is implemented correctly, there will be only one instance.

One implementation would be with a **public final field**:
```
// Singleton with public final field
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public void leaveTheBuilding() { ... }
}
```

Another possible implementation is with a **static factory**:
```
// Singleton with static factory
public class Elvis {
    private static final Elvis INSTANCE = new Elvis(); 
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; }
    public void leaveTheBuilding() { ... }
}
```

A third approach is by using an **enum type**:
```
// Enum singleton - the preferred approach
public enum Elvis {
    INSTANCE;
    public void leaveTheBuilding() { ... }
}
```

The implementation with **public final field** has the advantage that it makes it clear that the class is a *Singleton*.

The implementation with **static factory** has the advantage of giving the ability to stop using *Singleton* without changing the API.

The implementation with the **enum type** has the advantage of being more concise, providing the mechanics for serialization for free and a guarantee against multiple implementations. **It is the best way to implement a *Singleton***

To implement a *Singleton* class that implements the *Serializable* interface, the class must declare all instance fields `transient` and provide a `readResolve` method (item 77). Otherwise each time the object is deserialized, a new instance is created. An implementation of of this method would be:
```
// readResolve method to preserve singleton property
private Object readResolve() {
    // Return the one true Elvis and let the garbage collector 
    // take care of the Elvis impersonator.
    return INSTANCE;
}
```

## Item 4 - Enforce noninstantiability with a private constructor

Utility classes are generally defined not to be instantiated, but when there is an absence of explicit constructors the compiler provides a *parameterless constructor*. To avoid this, the class can implement a **private constructor** such as this:
```
// Noninstantiable utility class
public class UtilityClass {
    // Suppress default constructor for noninstantiability
    private UtilityClass() {
        throw new AssertionError();
    }
    ... // Remainder omitted
}
```

This private constructor guarantees that the class won't be instantiated, and as a side effect, this class cannot be subclassed because it is required to implement a constructor from a super class.

## Item 5 - Avoid creating unnecessary objects

Reusing an object is faster and more stylish, an object can be reused if it is *inmutable*.

An example of what **NOT TO DO** is:
```
String s = new String("stringette"); // DON'T DO THIS!
``` 
because it creates a new instance of String every time it is executed. Instead, this can be done:
```
String s = "stringette";
```
this case creates only 1 instance of the String, and in case it happens that the literal is used in another place, it will also be reused.

Creation of unnecessary objects can generally be prevented by using a *static factory methods* ([Item 1](#Item-1---Consider-static-factory-methods-instead-of-constructors)) in preference to constructors on inmutable classes that provide both.

Mutable objects can be reused in case it is known they won't be modified, for example, changing this:
```
public class Person {
    private final Date birthDate;
    // Other fields, methods, and constructor omitted
    // DON'T DO THIS!
    public boolean isBabyBoomer() {
        // Unnecessary allocation of expensive object
        Calendar gmtCal =
        Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0); 
        Date boomStart = gmtCal.getTime(); 
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0); 
        Date boomEnd = gmtCal.getTime();
        return birthDate.compareTo(boomStart) >= 0 && birthDate.compareTo(boomEnd) < 0;
    }
}
```
to this:
```
class Person {
    private final Date birthDate;
    // Other fields, methods, and constructor omitted
    /* The starting and ending dates of the baby boom. */
    private static final Date BOOM_START; 
    private static final Date BOOM_END;
    static {
        Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT")); 
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0); 
        BOOM_START = gmtCal.getTime();
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0); 
        BOOM_END = gmtCal.getTime();
    }
    public boolean isBabyBoomer() {
        return birthDate.compareTo(BOOM_START) >= 0 && birthDate.compareTo(BOOM_END) < 0;
    }
}
```

Another way to create unnecessary objects is by *Autoboxing*, which allows the programmer to mix primitive and non-primitive types together. The way to solve this is to **prefer primitives(int, double) to boxed primitives(Integer, Double) and watch out for unintentonal *Autboxing***.

Also, creating your own *object pool* is a bad idea unless the objects in the pool are extremely heavyweight, the modern JVM implementations have highly optimized object pools por lightweight objects.

## Item 6 - Eliminate obsolete object references

When using a language with a Garbage Collector such as Java, there is a common misconception that there is no need to take care of the memory management

Common sources of memory leaks:
 - Programs that manage it's own memory -> Anulate *obsolete references* by using `null` in those references.
 - Caches -> Use `WeakHashMap` so that if the key is externally referenced, it will continue to live the entry.
 - Listeners and other callbacks -> Use `WeakHashMap` to store references by storing the keys, in order to be able to deregister the callbacks correctly.

This Stack manages it's own memory, and it contains memory leaks:
```
// Can you spot the "memory leak"?
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;
    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }
    
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    
    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    }
    /**
    * Ensure space for at least one more element, roughly
    * doubling the capacity each time the array needs to grow. */
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    } 
}
```

The memory leak occurs when the stack grows or shrinks, the objects popped from the stack will not be garbage collected. The stack mantains an *obsolete reference* to those objects.

The fix for this problem would be this, even though it is nor desirable:
```
public Object pop() {
    if (size == 0)
        throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference return result;
}
```

An added advantage of this is that if a reference to the item is dereferenced, it will give a `NullPointerException`, hinting at an error in the program.

Whenever a program *manages it's own memory*, the programmer should be alert for memory leaks.

The best way to detect a memory leak is with a *heap profiler*.

## Item 7 - Avoid finalizers

Finalizers are often dangerous, unpredictable and generally unnecessary. 

As it can take arbitray long time between the time an object becomes unreachable and the finalizer code executed, **nothing time-critical should be done in a finalizer**.

The use of finalizers can affect the performance of the Garbage Collector and there is a **severe performance penalty of using finalizers**.

Instead, one can provide an *explicit termination method* required to be called when a client instance is not needed anymore. Examples of this are the `close` methods on InputStream, OutputStream and java.sql.Connection and the `cancel` method on a java.util.Timer.

Using a try finally block can guarantee the execution of the termination explicit method:
```
// try-finally block guarantees execution of termination method
Foo foo = new Foo(...); 
try {
    // Do what must be done with foo ...
} finally {
    foo.terminate(); // Explicit termination method
}
```

Some legitimate uses of finalizers are:
 - Safety net if the user forgets to call the termination method
 - Objects with *native peers*(object to which a normal object delegates native methods)

If a class has a finalizer and a subclass overrides the finalize method, that method should call the method from the super class:
```
// Manual finalizer chaining
@Override protected void finalize() throws Throwable {
    try {
        ... // Finalize subclass state 
    } finally {
        super.finalize();
    } 
}
``` 

One way to avoid careless client implementations is to use a *Finalizer Guardian*, but it comes at the expense of creating a new object:
```
// Finalizer Guardian idiom
public class Foo {
    // Sole purpose of this object is to finalize outer Foo object
    private final Object finalizerGuardian = new Object() {
        @Override protected void finalize() throws Throwable {
        ... // Finalize outer Foo object 
        }
    };
    ... // Remainder omitted
}
```