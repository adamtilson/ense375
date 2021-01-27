## ENSE 375 - Software Testing and Validation - Laboratory

# Lab 1: Introduction to Java

### University of Regina
### Faculty of Engineering and Applied Science - Software Systems Engineering

### Lab Instructor: [Adam Tilson](mailto:Adam.Tilson@uregina.ca)

---
## Introduction

This course will make use of the Java programming language. Java (not to be confused with JavaScript) is a high-level object oriented programming language, which is cross-platform, free and open source. This lab will walk you through the basics of creating and running a simple Java application assuming you are familiar with C++. Unlike C++, in which code is compiled to run directly on the CPU, Java is compiled to bytecode and run on a virtual machine. 

---
## Installation

[Download Java OpenJDK](https://jdk.java.net/15/)

Unzip somewhere you can find it and have permissions to use.

Optionally, add the `bin` folder to your environment PATH.

Versions: Java is backwards compatible, making it safe to use the newest version. We will only be using core features which have been supported since Java 8. 

If you like IDEs, give [VS Code](https://code.visualstudio.com/) (with [this](https://marketplace.visualstudio.com/items?itemName=redhat.java) and [this](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) extension) or [IntelliJ IDEA Community](https://www.jetbrains.com/idea/download/) a try. 

---
## Hello Java World!

The following snippet is a Hello World application in Java:

~~~java
public class HelloWorld {
    public static void main (String[] args) {
        System.out.println("Hello Java World!");
    }
}
~~~

1. Save as `HelloWorld.java`. You must use the same name as the class.
2. Compile with `javac HelloWorld.java`
3. Run with `java HelloWorld`

---
## Breaking it down
- `public` - Access modifier keyword. 
- `class` - All Java applications are classes, bundling variables and methods.
- `HelloWorld` - User-specified name of our class. Must be the same as the Java filename.
- `public static void main (String[] args)` - The function signature for our "main" function. Code in here will run if you try to run this class as an application.
- `System.out.println("Hello Java World!");` prints "Hello Java World!" to the console.

---

## Language Overview
- [Examples](https://introcs.cs.princeton.edu/java/11cheatsheet/)
    - Frame these in terms of what you know from other languages. You can ignore any of their APIs. 
- [Read the Docs!](https://docs.oracle.com/en/java/javase/15/)
    - For reference

---

## Language Specifics

This lab assumes that, as this is not your first programming language, most of the features will be easy to pick up. This section outlines some of the features that Java handles differently from other languages, which may take more time to familiarize yourself with.

---

### Primitives vs References and Memory Management.


Java has eight primitive variable types, for which the data is stored directly on the stack. These are: 

- int-like (int, byte, short, long)
- float-like (float, double)
- char
- boolean

All other data types are references, which point to objects, i.e., the address is stored on the stack and the actual data is stored on the heap using dynamic memory management. This is similar to pointers in C++, except the syntax is different and the data management is automatic.

As long as one or more references to an object on the heap exists, that object is preserved. When all references are removed, that memory will be automatically freed through garbage collection.

Examples:

This is a primitive, allocated in the stack

~~~java
int x = 3;
~~~

This is a reference to a value on the heap

~~~java
String helloWorld = new String("Hello Earth!");
~~~

We can delete this data on the heap by assigning a new value to the reference...

~~~java
helloWorld = new String ("Hello Mars!");
~~~

Or by assigning nothing to the reference...

~~~java
helloWorld = null;
~~~

If any hanging references remain, the heap memory will not be deleted:

~~~java
helloWorld = new String("Hello Jupiter!");
String helloAnotherWorld = helloWorld;
~~~

These Strings both point to the same place in memory, so if we try to remove one...

~~~java
helloWorld = null;
~~~

The string still exists, because `helloAnotherWorld` is also pointing to it, we will just no longer be able to access it through the first reference.

You can see this in action in the `eg-1` files.

You will get an opportunity to experiment with references in Exercise 3.

---
### Import Statements and Libraries

Java contains many built-in classes to accomplish common tasks, saving you time as a programmer. 

Here's an example of using the java Scanner library to get info from the user:

```java
import java.util.Scanner;

public class Main {
    public static void main (String[] args) {
        System.out.println ("Enter an int:");
        Scanner scanner = new Scanner (System.in);
        int userNumber = scanner.nextInt();
        scanner.close();
        System.out.println ("You entered: " + userNumber);
    }
}
```

You can see this code in action in `eg-2`.

You will get an opportunity to experiment with this in Exercise 2.

---
### Java Classes

Everything in Java is stored in classes. Classes roughly comprise of:

- Variables (Attributes, Properties, Fields)
- Functions (Methods)
- Constructors

When coding inside the class, use `this` keyword to disambiguate variables and methods which belong to the specific class

Instantiate the class using the `new` keyword.

Public member functions and variables are accessed from instances of the class using dot notation. (eg. `myAddress.streetNumber`)

Example: creating a class

in `Address.java`
```java
public class Address {
    private int     streetNumber;
    private String  streetName;

    public   Address (int streetNumber, String streetName) {
        this.streetNumber = streetNumber;
        this.streetName   = streetName;
    }
    public String toString () {
        return this.streetNumber + " " + this.streetName;
    }
}
```

in `Main.java`
```java
public class Main {
    public static void main (String[] args) {
        Address myAddress = new Address (742, "Evergreen Terrace");
        System.out.println(myAddress.toString());
    }
}
```
Compile the entire application with `javac *.java` and run the application with `java Main`. You can demo this in the `eg-3` files.

`static` methods may be called without first instantiating the class. You may think of these like method libraries. (eg. the built-in `Math` library) 

---
### Inheritance and Polymorphism

We can also use inheritance on our address we previously defined:

```java
public class BusinessAddress extends Address {
    protected String businessName;

    public BusinessAddress (int streetNumber, String streetName, String businessName) {
        super(streetNumber, streetName);
        this.businessName = businessName;
    }
    
    @Override
    public String toString () {
        return super.toString()  + " (" +  this.businessName + ")";
    }
}
```

in `Main.java`, we'll use polymorphism to create our `BusinessAddress` as an `Address`

```java
public class Main {
    public static void main (String[] args) {
        Address myAddress = new BusinessAddress (1600, "Amphitheatre Parkway", "Google");
        System.out.println(myAddress.toString());
    }
}
```

Try it out in the `eg-4` files.

Java supports abstraction through Interfaces and Abstract Classes.

- [`interface`](https://www.w3schools.com/java/java_interface.asp) - contains abstract methods, but does not implement them, instead deferring the implementation to subclasses. An exception to this is using default implementations. Interfaces can only contain `public static final` attributes. If you plan to have data types in your subclasses, you should add appropriate getters and setters in the interface. You can think of an interface like a contract which the implementer must fulfill.

- [`abstract class`](https://www.w3schools.com/java/java_abstract.asp) - cannot be instantiated, must be extended. However, can contain abstract and concrete classes, as well as non-static, non-final fields.

A java class can implement multiple interfaces, but only extend a single class

In Exercise 2 you will implement polymorphism.

---

### Collections

Java provides several ways of grouping objects in a Collection. Collections are homogeneous data structures (like Arrays) but with better memory management and traversal options.

[ArrayList](https://www.w3schools.com/java/java_arraylist.asp)

ArrayLists act like Arrays, but leverage some of the benefits of a Linked List. 

Example:
~~~java
import java.util.ArrayList;

public class Main {
    public static void main (String[] args) {

        // ArrayLists only work with reference types, ie. non-primitives, 
        // so to create an ArrayList of <int> type, with is primitive,
        // instead use the <Integer> wrapper class, which wraps the primitive
        // in a reference.
        ArrayList<Integer> arrayList = new ArrayList<Integer>();
        arrayList.add(1);
        arrayList.add(2);
        arrayList.add(3);
        // get and print a single element
        System.out.println("Element at index 0: " + arrayList.get(0));
        System.out.println();
        // Print elements using a for-each loop
        System.out.println("Array list contents in order:");
        for (int i : arrayList) {
            System.out.println(i);
        }
        // count the elements:
        System.out.println();
        System.out.println("Array list size: " + arrayList.size());

        // remove an element:
        System.out.println();
        arrayList.remove(0); // remove index zero
        // The element at the zero index should now be 2
        System.out.println("Element at index 0 after removal: " 
                           + arrayList.get(0)); 
        System.out.println();
    }
}
~~~

[HashMap](https://www.w3schools.com/java/java_hashmap.asp)

HashMaps are like Arrays, but the entries are grouped in key, value pairs. In other languages these are called Associative Arrays or Dictionaries.

Example:

```java
import java.util.HashMap;

public class Main {
    public static void main (String[] args) {
               
        // HashMap lets you use one data type, a "key", to look up a second
        // data type, a Value
        HashMap <String, Integer> peopleAges = new HashMap <String, Integer>();

        peopleAges.put ("Mick", 77);
        peopleAges.put ("Pete", 75);
        peopleAges.put ("Richard", 80);

        // get Richard's age:
        System.out.println("Richard's age: " + peopleAges.get("Richard"));
        System.out.println();
        
        // looping with a for-key-in-keys style loop
        for (String i : peopleAges.keySet()) {
            System.out.println("Key (name): " + i + "    Value (age): " + peopleAges.get(i));
        }

    }
}
```

Both of these examples are in the `eg-5` files.

---

### Risky Code with Try/Catch

If your Scanner is looking for an integer, and receives something else, it will throw an error. Using this example, we can see how Try / Catch blocks work in Java for attempting to run risky code, and then gracefully handling errors:

```java
import java.util.Scanner;
import java.util.InputMismatchException;

public class Main {
    public static void main (String[] args) {
        System.out.println ("Enter an int:");
        Scanner scanner = new Scanner (System.in);
        int userNumber;
        try {
            userNumber = scanner.nextInt();
        } catch (InputMismatchException e) {
            //e.printStackTrace();
            System.out.println ("Not an int!");
            scanner.close();
            return;
        }
        scanner.close();
        System.out.println ("You entered: " + userNumber);
    }
}
```
This example is in the `eg-6` files.

---

## Exercises

These three exercises are not intended to be too challenging, instead intended to get you accustomed to the syntax of Java applied to common problems. If you find you are struggling heavily with these exercises, it is strongly recommended you reach out for help - these are intended to be reasonable for 3rd / 4th year SSE students. No starter code will be provided, to get you used to writing (and thinking about) the Java boilerplate and properly structuring your projects.

1. Fizz Buzz

Objective: To get comfortable with Java syntax

This exercise is a popular job interview question, and while deceptively simple, reveals a lot about how you code. Write a Java application named FizzBuzz which prints the numbers 1 to 100. However, if the number is divisible by 3, instead print the word "Fizz", if the number is divisible by 5, print the word "Buzz", and if the number is divisible by both 3 and 5, print the word "FizzBuzz".

Example output:
```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
FizzBuzz
16
...

Fizz
94
Buzz
Fizz
97
98
Fizz
Buzz
```

2. Polymorphic Monsters!

Objective: To explore Java classes, inheritance and polymorphism

In this exercise, you are going to create stubs for creatures for a tactical role playing game. Each of these monsters must be of a given type (humanoid, familiar, monster or undead) which should be implemented as an appropriate hierarchy of abstract classes which the concrete classes extend. Additionally they should have movement functionality and attack functionality of a given type, which should be represented as appropriate hierarchies of interfaces which the concrete classes implement.


| Class             | Creature Type   | Attack Type |   Movement Type
| ----------- | --- | --- | ---
| Knight      |  Humanoid   |  Physical    |    Walk
| Cleric      |  Humanoid   |  Magical     |    Walk
| Skeleton    |  Undead     |  Physical    |    Walk
| Ghost       |  Undead     |  Magical     |    Teleport
| Birdbeing   |  Humanoid   |  Physical    |    Fly
| Lizardbeing |  Humanoid   |  Physical    |    Walk
| Fairie      |  Familiar   |  Magical     |    Fly
| Gremlin     |  Familiar   |  Physical    |    Fly
| Dragon      |  Monster    |  Physical    |    Fly 
| Cyclops     |  Monster    |  Magical     |    Walk

A completed concrete class should have three functions: type, attack and move, each of which should be inheritted from a parent class, which will print to the console how each of the monsters are.

Examples:
```
Creature knight = new Knight();
```
Calling...
```java
knight.type();
```
Should print...
```
The Knight is humanoid.
```
Calling...
```java
knight.attack();
```
Should print...
```
The Knight does a physical attack.
```
Calling...
```java
knight.move();
```
Should print...
```
The Knight walks.
```
Hints:
- You may wish to make use of the `this.getClass().getName()` method.
- Your `Creature` class should implement the interfaces for Movement and Attacking.
- You should make use of default methods in your interfaces.
- Your concrete classes should not require any code besides extending an abstract class and implementing interfaces.

3. Doubly Linked List

Objective: To get used to classes and references in Java

Create a doubly linked list, that is, a data structure which is like a linked list but contains references for traversal both forward and backward. Do not use any built in collections for this. You should have two classes:

A node class represents a single entry in the list, containing a value, and a next and previous reference. The class should also contain functions for getting and setting values, and joining and severing connections to adjacent Nodes.

The DoublyLinkedList class manages the overall list, such as creating new lists, adding new nodes to the front and back, removing nodes from the front and back while returning their values, and printing the list both forwards and in reverse. The following blocks represent the classes using UML class diagram notation:

```
DoublyLinkedList
-----------------------
- head : Node
- tail : Node
-----------------------
+ prepend (input : int)
+ append (input : int)
+ popFront () : int
+ popBack () : int
+ printForward ()
+ printReverse ()
```

```
Node
---------------------------------
- previous : Node
- next : Node
- value : int
---------------------------------
+ Node (value : int)
+ joinNext (next : Node)
+ joinPrevious (previous : Node)
+ removeNext ()
+ removePrevious ()
+ getNext () : Node
+ getPrevious () : Node
+ getValue () : int
```


The following test code...


```java
List myList = new List();
myList.append(3);
myList.append(4);
myList.append(5);
myList.prepend(2);
myList.prepend(1);
System.out.println("Print the list forward:");
myList.printForward();
System.out.println();
System.out.println("Print the list backward:");
myList.printReverse();
System.out.println();
System.out.println("Remove and print the first element:");
System.out.println(myList.popFront());
System.out.println("Remove and print the last element:");
System.out.println(myList.popBack());
System.out.println();
System.out.println("Print the list forward:");
myList.printForward();
System.out.println();
System.out.println("Print the list backward:");
myList.printReverse();
```
Should print...
```
Print the list forward:
1
2
3
4
5

Print the list backward:
5
4
3
2
1

Remove and print the first element:
1
Remove and print the last element:
5

Print the list forward:
2
3
4

Print the list backward:
4
3
2
```

---

## Submission

Please submit your files to URCourses by the due date. Submit in one `.zip` file. Include the java files for the application, and include a `readme.txt` file with the commands for compiling and running your application.

Example directory (zipped):
```
a-tilson-sid-ense375-l1.zip
├── readme.txt
├── q1/
│   └── FizzBuzz.java
├── q2/
│   ├── Main.java
│   └── Support.java
⋮
etc.
```

Example commands in `readme.txt`:
```
q1:
javac ./FizzBuzz.java
java FizzBuzz

q2:
⋮
etc.
```

---

## References

Overview

R. Sedgewick & K. Wayne, *[Java Cheat Sheet](https://introcs.cs.princeton.edu/java/11cheatsheet/)*, Princeton CS, 2019.

Oracle, [JDK 15 Documentation](https://docs.oracle.com/en/java/javase/15/), 2020.

Text Resources

K. Sierra & B. Bates, *Head First Java.* 2nd Ed, O'Reilly Media, Inc, 2005, ch. 1-11.

W3Schools, [Java Tutorial](https://www.w3schools.com/java/), Refsnes Data, 2021.

Video Tutorials

K. Hodges, [Learning Java](https://www.lynda.com/Java-tutorials/Welcome-learning-Java/2825378/2296116-4.html), Lynda from linkedin, 2020.

D. Gassner. [Java Essential Training: Objects and APIs](https://www.lynda.com/Java-tutorials/Welcome/679628/704606-4.html), Lynda from linkedin, 2020.

Misc

M. Behler, *[Java Versions and Features](https://www.marcobehler.com/guides/a-guide-to-java-versions-and-features)*, 2020.
