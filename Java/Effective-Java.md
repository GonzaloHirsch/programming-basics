# Effective Java
This is a summary of the book "Effective Java, Second Edition" by Joshua Bloch.

It will have each item in the book, summarized as well as possible.

## Item 1 - Consider static factory methods instead of constructors
**Advantage 1** -> Static factory methods give the programmer the advantage of naming the factory methods in order to obtain clearer code, avoid having to look into the documentation.

**Advantage 2** -> Static factory methods avoid having to create an instance of the object each time they are called, they give the programmer the ability to user preconstructed instances in inmutable clases or cache previous instances. It can greatly improve performance if objects are large and are frequently requested.

**Advantage 3** -> Static factory methods can return any subtype of the class, gives more flexibility.

**Advantage 4** -> Static factory methods reduce the verbosity of creating parameterized type instances.

**Disadvantage 1** -> Providing only static factory methods makes classes without public/protected methods not able to be subclassed.

**Disadvantage 2** -> Static factory methods not readily distinguishable from other static methods, cannot determine if it is a new instance easily. Some great common names are `valueOf`, `of`, `getInstance`, `newInstance`, `getType` and `newType`.

## Item 2 - Consider a builder when faced with many constructor parameters
Static factories and constructors do not scale well with optional parameters.

The *telescoping constructor* is not scalable -> It creates a constructor with the required parameters and many with the different optional parameters.