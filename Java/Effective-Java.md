# Effective Java
This is a summary of the book "Effective Java, Second Edition" by Joshua Bloch.

It will have each item in the book, summarized as well as possible.

## Index
These are all the items present in the summary (chapter 1 is an introduction):
 * [Chapter 2 - Creating and Destroying Objects](#chapter-2---creating-and-destroying-objects)
   - [Item 1 - Consider static factory methods instead of constructors](#item-1---consider-static-factory-methods-instead-of-constructors)
   - [Item 2 - Consider a builder when faced with many constructor parameters](#item-2---consider-a-builder-when-faced-with-many-constructor-parameters)
   - [Item 3 - Enforce the singleton property with a private constructor or an enum type](#item-3---enforce-the-singleton-property-with-a-private-constructor-or-an-enum-type)
   - [Item 4 - Enforce noninstantiability with a private constructor](#item-4---enforce-noninstantiability-with-a-private-constructor)
   - [Item 5 - Avoid creating unnecessary objects](#item-5---avoid-creating-unnecessary-objects)
   - [Item 6 - Eliminate obsolete object references](#item-6---eliminate-obsolete-object-references)
   - [Item 7 - Avoid finalizers](#item-7---avoid-finalizers)
  * [Chapter 3 - Methods Common to All Objects](#chapter-3---methods-common-to-all-objects)
    - [Item 8 - Obey the general contract when overriding equals](#item-8---obey-the-general-contract-when-overriding-equals)
    - [Item 9 - Always override hashCode when you override equals](#item-9---always-override-hashCode-when-you-override-equals)
    - [Item 10 - Always override toString](#item-10---always-override-toString)
    - [Item 11 - Override clone judiciously](#item-11---override-clone-judiciously)
    - [Item 12 - Consider implementing Comparable](#item-12---consider-implementing-comparable)
  * [Chapter 4 - Classes and Interfaces](#chapter-4---classes-and-interfaces)
    - [Item 13 - Minimize the accessibility of classes and members](#item-13---minimize-the-accessibility-of-classes-and-members)
    - [Item 14 - In public classes, use accessor methods, not public fields](#item-14---in-public-classes,-use-accessor-methods,-not-public-fields)
    - [Item 15 - Minimize mutability](#item-15---minimize-mutability)
    - [Item 16 - Favor composition over inheritance](#item-16---favor-composition-over-inheritance)
    - [Item 17 - Design and document for inheritance or else prohibit it](#item-17---design-and-document-for-inheritance-or-else-prohibit-it)
    - [Item 18 - Prefer interfaces to abstract classes](#item-18---prefer-interfaces-to-abstract-classes)
    - [Item 18 - Prefer interfaces to abstract classes](#item-18---prefer-interfaces-to-abstract-classes)
    - [Item 19 - Use interfaces only to define types](#item-19---use-interfaces-only-to-define-types)
    - [Item 20 - Prefer class hierarchies to tagged classes](#item-20---prefer-class-hierarchies-to-tagged-classes)
    - [Item 21 - Use function objects to represent strategies](#item-21---use-function-objects-to-represent-strategies)
    - [Item 22 - Favor static member classes over nonstatic](#item-22---favor-static-member-classes-over-nonstatic)
  * [Chapter 5 - Generics](#chapter-5---generics)
    - [Item 23 - Do not use raw types in new code](#item-23---do-not-use-raw-types-in-new-code)

# Chapter 2 - Creating and Destroying Objects
[(Back)](#index)

## Item 1 - Consider static factory methods instead of constructors
[(Back)](#index)

**Advantage 1** -> Static factory methods give the programmer the advantage of naming the factory methods in order to obtain clearer code, avoid having to look into the documentation.

**Advantage 2** -> Static factory methods avoid having to create an instance of the object each time they are called, they give the programmer the ability to user preconstructed instances in inmutable clases or cache previous instances. It can greatly improve performance if objects are large and are frequently requested.

**Advantage 3** -> Static factory methods can return any subtype of the class, gives more flexibility.

**Advantage 4** -> Static factory methods reduce the verbosity of creating parameterized type instances.

**Disadvantage 1** -> Providing only static factory methods makes classes without public/protected methods not able to be subclassed.

**Disadvantage 2** -> Static factory methods not readily distinguishable from other static methods, cannot determine if it is a new instance easily. Some great common names are `valueOf`, `of`, `getInstance`, `newInstance`, `getType` and `newType`.

## Item 2 - Consider a builder when faced with many constructor parameters
[(Back)](#index)

Static factories and constructors do not scale well with optional parameters.

The *telescoping constructor* is not scalable -> It creates a constructor with the required parameters and many with the different optional parameters.

The *JavaBeans pattern* allows for inconsistencies and requires for the programmet to take into account syncronization issues.

The solution is the **Builder Pattern**, the client calls a constructor (or static factory) with all of the required parameters and gets a builder object. Then the client calls setter-like methods on the builder object to set each optional parameter of interest. Finally, the client calls a parame- terless build method to generate the object, which is immutable.

The structure is the following:
```java
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
```java
// A builder for objects of type T
public interface Builder<T> {
    public T build();
}
```

The method `newInstance` from the `Class` object has a lot of issues, so it shouldn't be used. It may attempt to invoke a constructor that may not exist, it propagates exceptions thrown by the constructor used and the client code has to be mindful of `InstantiationException` and `IllegalAccessException` at runtime. It breaks compile-time exception checking, using builder corrects this mistakes.

## Item 3 - Enforce the singleton property with a private constructor or an enum type
[(Back)](#index)

A *Singleton* is a class that can be instanced only once.

There are 2 ways in which to implement the Singleton pattern, both based on private constructors and exporting a public static member in order to provide access to the instance. No public constructor guarantees that, if the pattern is implemented correctly, there will be only one instance.

One implementation would be with a **public final field**:
```java
// Singleton with public final field
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public void leaveTheBuilding() { ... }
}
```

Another possible implementation is with a **static factory**:
```java
// Singleton with static factory
public class Elvis {
    private static final Elvis INSTANCE = new Elvis(); 
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; }
    public void leaveTheBuilding() { ... }
}
```

A third approach is by using an **enum type**:
```java
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
```java
// readResolve method to preserve singleton property
private Object readResolve() {
    // Return the one true Elvis and let the garbage collector 
    // take care of the Elvis impersonator.
    return INSTANCE;
}
```

## Item 4 - Enforce noninstantiability with a private constructor
[(Back)](#index)

Utility classes are generally defined not to be instantiated, but when there is an absence of explicit constructors the compiler provides a *parameterless constructor*. To avoid this, the class can implement a **private constructor** such as this:
```java
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
[(Back)](#index)

Reusing an object is faster and more stylish, an object can be reused if it is *inmutable*.

An example of what **NOT TO DO** is:
```java
String s = new String("stringette"); // DON'T DO THIS!
``` 
because it creates a new instance of String every time it is executed. Instead, this can be done:
```java
String s = "stringette";
```
this case creates only 1 instance of the String, and in case it happens that the literal is used in another place, it will also be reused.

Creation of unnecessary objects can generally be prevented by using a *static factory methods* ([Item 1](#item-1---consider-static-factory-methods-instead-of-constructors)) in preference to constructors on inmutable classes that provide both.

Mutable objects can be reused in case it is known they won't be modified, for example, changing this:
```java
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
```java
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
[(Back)](#index)

When using a language with a Garbage Collector such as Java, there is a common misconception that there is no need to take care of the memory management

Common sources of memory leaks:
 - Programs that manage it's own memory -> Anulate *obsolete references* by using `null` in those references.
 - Caches -> Use `WeakHashMap` so that if the key is externally referenced, it will continue to live the entry.
 - Listeners and other callbacks -> Use `WeakHashMap` to store references by storing the keys, in order to be able to deregister the callbacks correctly.

This Stack manages it's own memory, and it contains memory leaks:
```java
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
```java
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
[(Back)](#index)

Finalizers are often dangerous, unpredictable and generally unnecessary. 

As it can take arbitray long time between the time an object becomes unreachable and the finalizer code executed, **nothing time-critical should be done in a finalizer**.

The use of finalizers can affect the performance of the Garbage Collector and there is a **severe performance penalty of using finalizers**.

Instead, one can provide an *explicit termination method* required to be called when a client instance is not needed anymore. Examples of this are the `close` methods on InputStream, OutputStream and java.sql.Connection and the `cancel` method on a java.util.Timer.

Using a try finally block can guarantee the execution of the termination explicit method:
```java
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
```java
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
```java
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

# Chapter 3 - Methods Common to All Objects
[(Back)](#index)

## Item 8 - Obey the general contract when overriding equals
[(Back)](#index)

There is no need to override the `equals` method if:
 - Each instance of the class is inherently unique, such as *Threads*, which are active entities insead of values
 - You don't care for the class to provide an equality test
 - A superclass has already overriden the method and the implementation fits your class
 - The class is package-private/private and you are sure the method will never be used

If the class is a *value class*(class that represents a value), it's fine to override the `equals`. It enables instances to serve as map keys or set elements with predictable behaviour.

For overriding the `equals`, the implementation must implemente an *equivalence relationship`:
 - *Reflexive* -> If `x` is not null, `x.equals(x)` is always true
 - *Symmetric* -> If `x` and `y` not null, `x.equals(y) == true` <=> `y.equals(x) == true`
 - *Transitive* -> If `x`, `y` and `z` not null, if `x.equals(y) == true && y.equals(z) == true` => `x.equals(z) == true` 
 - *Consistent* -> If `x` and `y` not null, all subsecuent calls will return the same value
 - If `x`is not null, `x.equals(null) == false`

There is no way to extend an instantiable class and add a value component while preserving the `equals` contract. To solve this there is a workaround by using the [Item 16](#) by favoring composition instead of inheritance.

The *Liskov substitution principle* states that any important property of a type should also hold for it's subtypes so that any method written on the type will work on the subtype.

The recipe for a correct override is:
 1. Use the `==` to check if the argument is a reference to the object
 2. Use `instaceOf` to check if the type is correct
 3. Cast the argument to the correct type
 4. For reach significant field, check if the fields match in the argument and the instance itself
     - If the field is `float` or `double` -> Use `Float.compare` or `Double.compare` respectively due to the definition of some constants
     - If the field is another primitive different to the previous -> Use `==`to check equality
     - If the field is an object -> User `equals` to compare
 5. After the method is implemented, ask yourself if it follows the rules stated previously

## Item 9 - Always override hashCode when you override equals
[(Back)](#index)

The `hashcode` method **must** be overriden in every class that overrides the `equals` method. If this is not done, the object will not work correctly in hash-based collections.

The contract for `hashcode` is as follows:
 1. As many times invoked for the same object, the hashcode must be the same.
 2. If 2 objects are the same according to the `equals` method, the `hashcode` method must return the same number.
 3. If 2 objects are the different according to the `equals` method, the `hashcode` method must return different numbers.

Equal objects **must** return the same hashcode.

The recipe for a correct override is:
 1. Store a constant, non-zero, prime integer in a variable `result`.
 2. For each significant field, `f`, in the object (fields used in equals method) compute a `c` value:
    - For `boolean` -> `f ? 1 : 0`
    - For `byte`, `char`, `short`, `int` -> `(int) f`
    - For `long` -> `(int)(f^(f>>>32))`
    - For `float` -> `Float.floatToIntBits(f)`
    - For `Object` -> Call `hashcode` method over object or `0` if the object is null
    - For `Array` -> Apply the rules to each element
 3. Combine the `c` value as follows -> `result = 31 * result + c`
 4. Test the method

An interesting optimization is to cache the value of the hashcode method, mainly if the object is expected to be used as a hash key. In case the object is heavy, this is a good practice. For example:
```java
// Lazily initialized, cached hashCode
private volatile int hashCode;  // (See Item 71)

@Override 
public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = 17;
        result = 31 * result + areaCode;
        result = 31 * result + prefix;
        result = 31 * result + lineNumber;
        hashCode = result;
    }
    return result;
}
```
 
## Item 10 - Always override toString
[(Back)](#index)

A general `toString` implementation should be "a concise but informative representation that is easy for a person to read”. Provinding a good `toString` implementation makes the class more pleasant to use.

It is a good practice to document the format of the `toString` for others to be able to work with it. Intentions should be documented. 

As a programmer, you should provide programatic access to all the information contained in the `toString` value.

## Item 11 - Override clone judiciously
[(Back)](#index)

The `clone` method creates a copy of an object without calling a constructor.

If you override the clone method in a nonfinal class, you should return an object obtained by invoking super.clone.

In practice, a class that implements Cloneable is expected to provide a properly functioning public clone method.

A possible implementation is to implemente the `Clonable` interface and in turn call the `Object.clone` method like this:
```java
@Override 
public PhoneNumber clone() {
    try {
        return (PhoneNumber) super.clone(); } 
    catch(CloneNotSupportedException e) {
        throw new AssertionError();  // Can't happen
    }
}
```
The idea is to never make the client do anything the library can do for the client, this is followed by casting the `super.clone` result to avoid having the client to cast it itself.

In case a class contains inner structures, it is necessary for the clone object to avoid harming the original object contents and avoid pointer relationships with the original object. For example:
```java
@Override 
public Stack clone() {
    try {
        Stack result = (Stack) super.clone(); 
        result.elements = elements.clone(); 
        return result;
    } catch (CloneNotSupportedException e) {
        throw new AssertionError();
    } 
}
```

In the previous case, this implementation would not work if the `elements` property is final.

A better approach is to implement a *copy factory* or a *copy constructor*

## Item 12 - Consider implementing Comparable
[(Back)](#index)

The `compareTo` is a method defined in the `Comparable` interface, it permits order and equality comparisons. 

Implementing the `Comparable` interface indicates the objects have a *natural ordering*. It also allows you to interoperate with collections with a minimal effort.

The contract for the `compareTo` method is as follows:
```
Compares this object with the specified object for order. Returns a negative integer, zero, or a positive integer as this object is less than, equal to, or greater than the specified object. Throws ClassCastException if the specified object’s type prevents it from being compared to this object.
```

Things the implementation should take care of (*sgn* is the mathematical sign function, it returns -1, 0 or 1):
 1. `sgn(x.compareTo(y)) == -sgn(y.compareTo(x))` for all `x` and `y`.
 2. Relation must be transitive --> `(x.compareTo(y) > 0 && y.compareTo(z) > 0)` implies `x.compareTo(z) > 0`
 3. `x.compareTo(y) == 0` implies that `sgn(x.compareTo(z)) == sgn(y.compareTo(z))`, for all `z`.
 4. It is also recommended that `(x.compareTo(y) == 0) == (x.equals(y))`

Being that the `Comparable` interface is parametrized, there is no need to perform type checks like the ones in the `equals` method, but if the argument is null, the invocation should throw a `NullPointerException`, and it will, as soon as the method attempts to access its members.

Compare integral primitive fields using the relational operators < and >. For floating-point fields, use `Double.compare` or `Float.compare` in place of the relational operators, which do not obey the general contract for compareTo when applied to floating point values. For array fields, apply these guidelines to each element.

If a class has multiple significant fields, the order in which you compare them is critical. You must start with the most significant field and work your way down.

# Chapter 4 - Classes and Interfaces
[(Back)](#index)

## Item 13 - Minimize the accessibility of classes and members
[(Back)](#index)

The most important factor that determines if a module/API is well-designed is how well it hides it's implementation details. This is known as *encapsulation* or *information hiding*.

Even though *encapsulation* might not cause good performance, it enables good performance tunning and speeds up the development process.

If a top-level class/interface can be made *package-private*, it should be. By being *package-private*, it allows you to modify, replace or delete it without harming the clients, because it is part of the implementation.

If a top-level *package-private* class/inteface is being used by only 1 class, consider making it a private nested class of that only class that uses it.

The 4 levels of accessiblity, in increasing order are:
 - **private** --> Only accessible from where it was declared
 - **package-private** --> Only accessible from inside the package it was declared in
 - **protected** --> Only accessible by subclasses of the class it was declared in
 - **public** --> Accessible from anywhere

There is 1 rule that restricts your ability to reduce access, if a method overrides a superclass method, it cannot have a lower access level than the original method.

*Instance fields or static fields should never be public* (not counting constants), and violating this, makes your implmentation not thread-safe.

## Item 14 - In public classes, use accessor methods, not public fields
[(Back)](#index)

In order to enforce *encapsulation*, you shoud make sure to *provide accessor methods if a class is accessible outside it's package*. However, if a class is package-private or private nested, there is nothing wrong with exposing it's methods.

## Item 15 - Minimize mutability
[(Back)](#index)

An *immutable class* is a class whose instances cannot be modified. They are easier to design, implement and use than mutable classes.

To make a class immutable follow these principles:
 1. Don't provide methods that mutate the object's state
 2. Ensure the class cannot be extended. One alternative is to make the class `final`
 3. Make fields final
 4. Make al fields private, prevent access to mutable objects
 5. Ensure exclusive access to mutable components, make sure clients cannot obtain instances of the inner mutable objects

Immutable objects are inherently thread-safe, as they cannot be modified, they don't need synchronization.

The real disadvantange of immutable objects is that they require a new instance for each sepparate value, meaning that creating a new instance of a big object is going to be costly.

How to implement an immutable object:
 - Private constructors with *static factories*
 - Lazy initialization of values the first time they are used, then they can be cached

A big thing to take away is that you should make the fields final unless you don't need to.

## Item 16 - Favor composition over inheritance
[(Back)](#index)

**Inheritance**, even though it is great to achieve code reuse, may be not the best tool for the job. Inheritance violates the principle of encapsulation, the new class depends on the implementation details of the superclass. If the superclass implementation changes, it can break the inherited class.

**Composition** is used to solve many of the issues generated by inheritance. It is based on internally using the class to be extended, for it to become a component of the new class.

Inheritance is appropiate when class B is a subtype of class A, you should be able to answer confidently the question "Is every B really an A?"

## Item 17 - Design and document for inheritance or else prohibit it
[(Back)](#index)

A class should document the *self-use* of overridable methods in order for the programmer to know the effects of overriding any method.

Designing for inheritance involves explaining the internal workings of the overridable methods and other methods that involve calling those overridable methods.

The only way to test a class designed for inheritance is by writing subclasses.

Constructors must not invoke overridable methods, it will generate failures and unexpected behaviours.

Designing for inheritance introduces substancial limitations to the classes. The best solution for a neither final nor designed/documented for subclassing, is to prohibit their subclassing. For this, there are 2 alternatives:
 1. Make the class final
 2. Make the constructors private and use static factories

## Item 18 - Prefer interfaces to abstract classes
[(Back)](#index)

Existing classes can be modified to implement a new interface, but cannot be modified to extend a new class.

Interfaces are a good way of defining mixins, which are optional behaviours.

You can combine **interfaces** and **abstract classes** to create *skeletal implementations*, known as **Abstract***Interface*. The interface defines the type, and the abstract class takes up most of the work of implementing it. Here, *Interface* is the name of the interface, being so that for example, the names are *AbstractSet* and *AbstractMap*. These implementations are designed for inheritance, so previous items should be taken into account.

Abstract classes are far easier to extend, you cannot modify an interface without breaking all it's clients.

## Item 19 - Use interfaces only to define types
[(Back)](#index)

When a class implements an interface, the interface serves as the *type* that can be used to refer to the instances of the class.

*Constant Interfaces* are interfaces that don't follow the previous, they only contain constants, no methods. **This is a poor use of interfaces.** Interfaces should be used to define types, not constants.

If constants need to be exported, a utility class can be used such as this:
```java
// Constant utility class
package com.effectivejava.science;

public class PhysicalConstants {
    private PhysicalConstants() { }  // Prevents instantiation

    public static final double AVOGADROS_NUMBER = 6.02214199e23; 
    public static final double BOLTZMANN_CONSTANT = 1.3806503e-23; 
    public static final double ELECTRON_MASS = 9.10938188e-31;
}
```

Combined with a static import to avoid the qualifying class:
```
import static com.effectivejava.science.PhysicalConstants.*;
```

## Item 20 - Prefer class hierarchies to tagged classes
[(Back)](#index)

*Tagged classes* are classes that containt a *tag* indicating the flavour of the instance, they are **verbose, error-prone and inefficient**, such as this:
```java
// Tagged class - vastly inferior to a class hierarchy!
class Figure {
    enum Shape { RECTANGLE, CIRCLE };

    // Tag field - the shape of this figure 
    final Shape shape;

    // These fields are used only if shape is RECTANGLE
    double length;
    double width;

    // This field is used only if shape is CIRCLE
    double radius;

    // Constructor for circle
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius; }
        
    // Constructor for rectangle
    Figure(double length, double width) {
        shape = Shape.RECTANGLE; 
        this.length = length; 
        this.width = width;
    }

    double area() {
        switch(shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError();
        }           
    }
}
```

This can be replaced with class hierarchy:
```java
// Class hierarchy replacement for a tagged class
abstract class Figure {
    abstract double area();
}

class Circle extends Figure {
    final double radius;
    Circle(double radius) { 
        this.radius = radius; 
    }
    double area() { 
        return Math.PI * (radius * radius); 
    }
}

class Rectangle extends Figure {
    final double length;
    final double width;
    Rectangle(double length, double width) { 
        this.length = length;
        this.width = width;
    }
    double area() { 
        return length * width; 
    }
}
```

## Item 21 - Use function objects to represent strategies
[(Back)](#index)

*Function Objects* are objects that have methods that performs an operation on some given other objects, for example:
```java
// A reference to this StringLengthComparator is a Function Object to the compare method
class StringLengthComparator implements Comparator<String> {
    public int compare(String s1, String s2) {
        return s1.length() - s2.length(); 
    }
}
```

Anonymous classes can be used as Function Objects, but they are inefficient, a new instance is created each time the code is executed, so it's better to use the Singleton pattern to avoid generating instances.

## Item 22 - Favor static member classes over nonstatic
[(Back)](#index)

A *Nested Class* is a class defined inside another class. There are 4 types of nested classes, *static member classes*, *nonstatic member classes*, *anonymous classes*, and *local classes*. All but the first are known as *inner classes*.

A *static member class* is a class defined inside an enclosing class, has the *static* modifier and has access to all the fields of the enclosing class.

A *nonstatic member class* is very similar to a *static member class*, with the only difference that each instance of the nonstatic member is associated to an instance of the enclosing class.

If you declare a member class that does not require access to the enclosing instance, make it *static*.

# Chapter 5 - Generics
[(Back)](#index)

## Item 23 - Do not use raw types in new code
[(Back)](#index)

*Raw Type* is the type that uses generics, but without the generics part, for example, the raw type of `List<E>` is `List`.

If you declare the variable/parameter like this:
```java
// Now a raw collection type - don't do this!
/* My stamp collection. Contains only Stamp instances. */
private final Collection stamps = ... ;
```

You lose all the safety that generics bring, the correct way would be:
```java
// Parameterized collection type - typesafe 
private final Collection<Stamp> stamps = ... ;
```

Raw types are allowed only for backwards compatibility with legacy Java code.

The difference between `List` and `List<Object>` is that the first one loses the type safety, while the second one is safe.

The difference between `List` and `List<?>` is that the first one is not safe while the first one is.