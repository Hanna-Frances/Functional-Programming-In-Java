# Functional Programming in Java 

**Introduction**

In this article, we will go over functional programming in Java. For the
purpose of the article, it will be assumed that you have foundational
knowledge up to Java 7. All of the features I will talk about have been
introduced in Java 8 onwards.

**What is Functional Programming**

To begin, it is important to have a solid understanding of what
functional programming is and its advantages. Functional Programming is
a programming paradigm that takes huge emphasis on the structure of
functions, and applications of their use as objects. At the same time,
emphasis is put on ensuring the code is safe to use anywhere, even in
parallel processes. This is done by the creation of pure functions;
functions that don’t cause any global side effects and have consistent
outputs for the same input. As well, the use of immutable data
structures is recommended to avoid modification of data, which would
cause a global side effect. The emphasis on avoiding global side effects
makes functional programming ideal for a multi-threaded environment; you
don’t have to worry about weird interactions between threads because of
the isolated nature of the operations. As well, Functional programming
excels at cleanly and efficiently manipulating data, which is important
during an age in which data is becoming plentiful and critical for
society. This programming paradigm also has the concept of using
functions as objects, something Java never supported until version 8.
Subsequently, a function that takes other functions as parameters is
known as a “higher-order function”, and can help condense code in some
situations as to be shown later. This article will mostly focus on the
use of functions as objects and higher order functions, as using this
aspect of functional programming is not as intuitive as the others. It
is also worth noting that Java technically does not have functions, as
functions are not linked to any class or object. Java, being a strictly
object-oriented programming language, does not have a feature to create
a function outside of a class block. As you should know, Java has what
are called methods, which are identical to functions, but associated
with a class or object. For the purposes of this guide, however, this
distinction is not very significant and the terms “function” and
“method” may be used interchangeably.

**An Overview of Java’s Support for Functional Programming**

In its earlier versions, Java was never initially developed to support
functional programming; you could definitely create pure functions, and
have immutable objects and data structures, however there was no real
way to use functions as objects, and subsequently, create higher-order
functions. Java 8, however, provided the tools necessary to use these
features, which has some applications to really help the life of a Java
developer. These tools include the introduction of
UnmodifiableCollections, the lambda expression, Streams and method
reference.

**Unmodifiable Collections**

Ordinary Collections such as Lists are mutable by nature, and so if one
is not careful, an unwanted modification can be made. Within the
Collections class, there exists many static methods used to create
UnmodifiableCollections out of ordinary ones. These static methods take
the naming convention of: unmodifiable\<CollectionName\>, and take the
Collection object you wish to make unchangeable. Take the following
example:

```java
List<String> strings = Collections.unmodifiableList(new ArrayList<>(Arrays.asList("Hello", "World")));  
  
strings.add("Illegal");  
  
System.out.println(strings);
```

We convert an otherwise modifiable List into an unmodifiable version.
Attempting to run this example would throw
UnsupportedOperationException, preventing you from modifying the
Collection as attempted here.

Even though an Exception is thrown for adding or removing elements, it
is not guaranteed the Collection is immutable. For instance, take the
following example (note that the Point Class is from the java.awt
package):

```java
Point a = new Point(5,9);  
  
List<Point> points = Collections.unmodifiableList(new ArrayList<>(Arrays.asList(a)));  
a.setLocation(20,20);  
  
System.out.println(points);
```

Running the code outputs the following:
```
[java.awt.Point[x=20,y=20]]
```

As you can see, an object within the List was modified using a reference
pointing to the same object. To ensure that the List is truly immutable,
it must only contain objects that are also immutable.

Note Java 9 added a less verbose way to create these immutable
Collections, following the basic syntax:

```java
List<String> strings = List.of("Hello", "World");
```

There are many other static methods within the Collections classes of
this fashion, such as corresponding methods for Maps and Sets.

**Lambda Expressions**

A Lambda Expression can simply be defined as an expression used to
cleanly create a method in-line, with the consequence that it sacrifices
the re-usability of a typical method. These lambda expressions have
object-like qualities in that they are passed into higher-order
functions (they are actually in fact their own objects but that will be
explained later). As an example of the syntax of these expressions, say
we had this simple method:

```java
public static boolean isEven(int num){
    return num % 2 == 0;
}
```

This same basic logic can be expressed as a lambda expression as
follows:

```java
(num) -> { return num % 2 == 0; }
```

The arguments are written with brackets whereas the method body is
written within curly brackets, these two separated with a "-\>".

Since the there is only one argument within the brackets, the expression
can be simplified further:

```java
num -> { return num % 2 == 0; }
```

As well, since the method body only contains a return statement, it may
be even further simplified:

```java
num -> num % 2 == 0;
```

At this point, you may be wondering why the types of the parameter and
returned value are omitted. With Java being a statically typed language,
it may not be very intuitive to you as to why this is the case. However,
Lambda Expressions are indeed strongly and statically typed, even though
types aren't explicitly shown, this will be explained in more detail
later.

**Lambda Expressions vs Anonymous Inner Classes**

Lambda expressions, being a way to create “methods” on the fly, draws
some comparison to anonymous inner-classes, a way to build objects on
the fly. To review, anonymous inner-classes are often used to pass event
listeners into swing components:

```java
JButton b1 = new JButton("Hello World");

b1.addActionListener(new ActionListener() {

    public void actionPerformed(ActionEvent e) {
    
        System.out.println("This is pretty cool");
        
    }
    
});
```

With the anonymous inner-class, we create the class in-line as it does
not have any re-usability afterwards. Lambda expressions actually work
in almost the exact same way; they are also used to create objects on
the fly to be used only once, just as the anonymous inner-class. These
objects are essentially instances of wrapper classes for the methods we
are interested in using. Behind the scenes, lambda expressions are just
implementations of what are called “functional interfaces”.

**Functional Interfaces**

Functional interfaces are essentially just interfaces that contain one
unimplemented method. The idea behind functional interfaces is that they
are meant to only represent a functionality and not some kind of entity.
These properties allow the compiler to map lambda expressions into their
corresponding functional interface that they implement.

If you carefully read that description, you may have been able to notice
that the ActionListener interface follows this description; it has only
one method to implement, and it represents a functionality, a response
to an action. In that case, you would be completely correct, the
ActionListener interface is a functional interface, and thus can be
passed as a lambda expression:

```java
JButton b1 = new JButton("Hello World");

b1.addActionListener(event -> {
    System.out.println("This is pretty cool");
});
```

Since there is only one method to implement, the only really meaningful
code is its implementation; the lambda expression allows us to only show
the implementation of that one method, removing boilerplate code.

Going back to the question about type safety, the compiler knows that
the lambda expression is really just an implementation of the
ActionListener interface, since the addActionListener method expects
one. Consequently, it knows that the type of the parameter must be of
ActionEvent, as the lambda expression implements a method requiring it.
The compiler also knows that the method being implemented has a return
type of void. If the method was supposed to return a String for example,
the compiler would expect the lambda to return a string, even without
explicitly saying that it should as we do with normal methods. Note:
functional interfaces often have their argument and return types as
generics, in which case the type of the arguments and return value would
be specified by the method accepting it.

**Stream api**

One important application of lambda expressions is to be passed into
higher order functions to manipulate the values within data structures.
It would be impossible to talk about this application without talking
about streams. A stream in this context can be thought of as a conveyor
belt; this conveyor belt takes objects from a source, typically some
kind of data structure, and then inputs the objects into methods. After
interacting with an object in this conveyor belt, you cannot go back to
that object and as it has already gone through. Thus, that conveyor belt
cannot really be used to store objects as they just pass through them as
a medium to be put into methods, and the conveyor belt doesn’t have a
capacity as it does not store objects. It is important to note that
since the objects from the stream can only be visited once, upon using a
stream it cannot be reused. If a stream is accidentally reused an
IllegalStateException is thrown.

Streams can be created out of Collections by invoking the stream method.
Using the stream, you can perform a variety of operations on the
elements within the Collection in a clean way. These streams abide by
the principle of not causing any side effects; they do not modify the
original Collection. Instead data is gathered into another source, which
is typically a new stream. Methods of the Stream interface can be
divided into two categories: intermediate and terminal methods. The
intermediate methods return new streams to further manipulate, whereas
terminal methods return a final result, or do something with it. As a
simple example, the code below takes a list and uses streams to create a
new list with all the odd elements removed.

```java
List<Integer> nums = List.of(2, 3, 4, 9, 11, 14, 16);
  
List<Integer> result = nums.stream()  
    .filter(num -> num % 2 == 0)  
    .collect(Collectors.toList());  
  
System.out.println(result);
```

As you can see, using streams for this problem provides an easy solution
that is very readable and concise. First, we create a stream of the list
of integers by invoking the stream method, then we invoke the filter
method using a simple lambda expression as an argument. This lambda
expression implements a functional interface called “Predicate.” The
method it implements takes a value (in this case, it is of type integer
as are the elements), and returns a boolean. The filter method inputs
each element into the method; if false is returned, then the element is
removed from the resulting stream. Note that since the intermediate
methods return new streams, we can chain methods of the stream interface
when performing multiple operations; each call within the chain
manipulates the elements of the resulting stream from the previous call.
By convention, we put each method in the chain on their own lines and on
separate indentation levels. After calling the filter method, we invoke
the collect method with the argument: “Collectors.toList()”, in order to
collect each element in the stream into a new list.

In case you may want to perform these operations asynchronously, you
have the option to obtain a parallel stream from a collection by
invoking the parallelStream method rather than the stream method.

**Some Common Stream Methods**

The Stream interface has a large amount of methods to provide useful
tools when manipulating data. Some of the more commonly used methods I
will go into detail about are the map, forEach and reduce methods of the
Stream interface. If you are interested in learning more about the
Stream interface, you should find all you need to know from the
documentation.

The map method takes each element from the stream, performs some
manipulation specified by the method argument, and then returns a new
stream of the resulting values. The resulting values do not necessarily
have to be of the same type of the original values. There also exists
some methods in the Stream interface that behave exactly like the map
method, but must return a Stream of a particular type. These methods
follow the basic naming convention of: mapTo\<DataType\>.

The forEach method takes each element of the Stream and expects you to
perform some kind of action with each element, such as a method
invocation. The return type of this method is void as it is a terminal
method.

The reduce method takes each element of the stream, cumulatively
performing an operation we define to reduce it into a single result.
For example, you may pass a lambda expression to add two elements,
returning the result. In this case the reduce method would return 
the sum of all the elements. Note: the lambda expression passed must
take two elements of the stream as arguments.

**Method Reference**

Going back to our description of lambda expressions, a lambda expression
is an expression used to create a “method” on the fly. The one drawback
is that lambda expressions lack the re-usability of normal methods. The
question then arises, what if you had a method you wish to have
re-usability, but also wanted the option to pass it into a higher-order
function? With method reference, you can pass ordinarily defined methods
into higher-order functions, allowing you to get the best of both
worlds. The basic syntax for method reference is as follows:
<Class/ObjectName>::\<method\>. Instead of using the ordinary dot syntax
to reference a method, two colons are used in order to specify that we
do not wish to call the method. You may even call the constructor of
classes using the following structure: \<ClassName\>::new. This would be
used to take elements of a stream, input each as constructor arguments,
and output a new stream of instances of the corresponding class. As an
example, say we had the following Person class:

```java
package com.john.amiscaray.functional.examples.models;  
  
public class Person {  
  
    private String name;  
  
    public Person(String name){  
  
        this.name = name;  
  
    }  
    public String getName() {  
        return name;  
    }  
    public void introduce(){  
  
        System.out.println("Hello my name is " + name);  

    }  
  
}
```

If we had a list of names we wanted to create Person objects out of, and
then invoke the introduce method on each of them, we can solve this
problem trivially using higher-order functions and method reference:

```java
List<String> names = List.of("John", "Dane", "Jane");  
  
names.stream()  
    .map(Person::new)  
    .forEach(Person::introduce);
```

Here, we first create a stream out of the list of names, then we call
the map method to create Person instances, each String being used as the
name field. Then we call the forEach method to invoke the introduce
method of each Person. Since it is being applied on a stream of Person
objects, we add the introduce method as an argument, which would invoke
the introduce method on each Person. That is the equivalent of doing the
following:

```java
.forEach(person -> person.introduce());
```

**Summary and Conclusion**

To summarize all that was discussed in this article, we went over the
basic idea behind functional programming, and some of the tools Java
gives us to code in this style. This includes UnmodifiableCollections,
functional interfaces, lambda expressions, stream api and method
reference. Knowing how to use these tools properly gives us versatility
to code in a functional style, which may be most effective in some
scenarios. 
