## ENSE 375 - Software Testing and Validation - Laboratory

# Lab 2: Introduction to JUnit

### University of Regina
### Faculty of Engineering and Applied Science - Software Systems Engineering

### Lab Instructor: [Adam Tilson](mailto:Adam.Tilson@uregina.ca)

---

"Programs should written for people to read, and only incidentally, for machines to execute" - Structure and Interpretation of Computer Programs, Abelson and Sussman

---
## What is a Unit Test?

Unit - A single, identifiable, piece of logic. i.e., a method, set of methods or a class.

Unit Test - A test to validate a unit is working correctly.

- A unit test corresponds to a single scenario for a single piece of logic.

- For each unit there are typically many unit tests.

You may contrast this approach with testing a system end-to-end, e.g. including external modules and components like databases. This is called integration testing, and is not what we will be focusing on in this lab.

A unit test should be fast and repeatable. A complete set of unit tests should be easy to run, complete and up-to-date. Together these will give you courage to refactor bad code into good code without fear of breaking functionality.

---
## What is JUnit?

`JUnit` is part of the `xUnit` family of testing frameworks. The major components are a `Test Case`, code you will write to test your application, and `Test Runners`, code which runs the tests and reports their outcomes. Test Cases are built mainly upon `Assertions`, functions which verify behaviour and throw an exception if the behaviour is not met.

The most recent version of JUnit is JUnit 5, from 2017. It significantly changed the architecture from JUnit 4, breaking it into three components: JUnit Platform, JUnit Jupiter and JUnit Vintage. 
- JUnit Platform provides an API for writing tests
- JUnit Jupiter includes tools for running tests
- Junit Vintage includes tools for supporting legacy tests, e.g. Junit 3 and 4.

---
## Installing JUnit

We can download the entire Junit 5 as a Java `.jar` (~2MB) and add it directly to the project dependencies. 

You can grab it from [Maven](https://search.maven.org/search?q=a:junit-platform-console-standalone) and download the "all.jar" version.

At the time of this lab, the current version is:
```
junit-platform-console-standalone-1.7.0-all.jar
```

---
## Project Setup

As your project will now have source files and tests, it makes sense to use a common directory structure. We will use:
```
project-name/
├── readme.md
├── junit-platform-console-standalone-1.7.0-all.jar
├── bin/
│   ├── DemoTest.class
│   └── Demo.class
├── src/
│   └── Demo.java
└── test/
    └── DemoTest.java
```

---
## Your First Test

Save your first test in /test/DemoTest.java

```java
import org.junit.jupiter.api.Test; //JUnit5
import static org.junit.jupiter.api.Assertions.fail; // Always fails

public class DemoTest {
    @Test
    public void alwaysFail() {
        fail();
    }
}
```

compile with 
```
javac -d bin -cp "junit-platform-console-standalone-1.7.0-all.jar" ./test/DemoTest.java
```

and run with
```
java -jar junit-platform-console-standalone-1.7.0-all.jar -cp bin --scan-class-path
```

This should find and run your test and report the result.

You can see this example in `eg-1`. Be sure to add your copy of JUnit to the project directory.

Breaking these commands down, the -d option shows the output directory where our compiled files will go, in this case the binary (bin) directory.

The -cp option, which represents class path, shows the place that javac will look for dependencies and libraries. In this case, we are treating JUnit as a library. You can look in multiple places by separating them with the `;` key on Windows, or `:` in Linux. Later we will need to look for both our original compiled binaries as well as classes inside the JUnit 5 Jar.

---
## Testing our own code

Let's create a very simple Calculator app, and add a simple test that ensures it is adding correctly...

in `/src/Calculator.java`
```java
public class Calculator {
    public int add (int a, int b) {
        return a + b;
    }
}
```
in `/test/CalculatorTest.java`
```java
import org.junit.jupiter.api.Test; //JUnit5
import static org.junit.jupiter.api.Assertions.assertEquals;
public class CalculatorTest {
    @Test
    public void Add_SimpleAddition_ReturnSum() {
        Calculator calculator = new Calculator();
        int result = calculator.add(3, 4);
        assertEquals(result, 7);
    }
}
```

---
A note on naming tests:

Typically tests have long, descriptive names. We will not be using these names directly, since the tests are run automatically by a framework, so longer, standard names are preferred as they make it quicker for us to find our problem when these tests fail. Thus, we'll use the following standard (Osherove):

```
UnitOfWork_StateUnderTest_ExpectedBehavior
```
---

We now have a slightly more complicated compiling and running procedure. 

Compile our code:
```
javac -d bin ./src/Calculator.java
```
Compile tests on Windows:
```
javac -d bin -cp "bin;junit-platform-console-standalone-1.7.0-all.jar" ./test/CalculatorTest.java
```
Compile tests on Linux:
```
javac -d bin -cp 'bin:junit-platform-console-standalone-1.7.0-all.jar' ./test/CalculatorTest.java
```

Run the tests with:
```
java -jar junit-platform-console-standalone-1.7.0-all.jar -cp bin --scan-class-path
```

Which will tell the JUnit 5 test runner to scan the classpath for all tests in the bin directory and run them. The output will be shown in the console.

You can see this code in action in `eg-2`.

---

## Assertions

From here, it is a good idea to take a run through the different Asserts to see what JUnit 5 is capable of. You can find these [here](https://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/api/Assertions.html). Some of the major families of assert functions:

- assertEquals
- assertNotEquals
- assertTrue
- assertFalse
- assertNull
- assertNotNull
- assertSame
- assertNotSame
- assertThrows

For each of these, we may add an additional String argument at the end which will print to the console if the test fails.

---
## Testing for Exceptions

Testing for exceptions is easier in JUnit 5 than in JUnit 4, thanks to the assertThrows function, as shown in the following example:

In Read.java
```java
public class Read {
    private int number;
    public Read (String number) {
        this.number = Integer.parseInt(number);
    }
}
```

In ReadTest.java
```java
import org.junit.jupiter.api.Test; //JUnit5
import java.lang.NumberFormatException;
import static org.junit.jupiter.api.Assertions.assertThrows;

public class ReadTest {
    @Test
    public void ReadConstructor_NonNumericInput_ThrowsError() {
        assertThrows(NumberFormatException.class, 
                     () -> {  //anonymous function
                         new Read("Num");
                     });
    }
}
```

You can try this example in `eg-3`.

---

## Assignment

A local family restaurant is world-famous for its 'burger of the day', in which every day a new hamburger is created from a unique combination of toppings, and given a punny name. These burgers are always sold for $5.95, however the cost for each burger changes depending on the ingredients used. You are to create an application using object oriented programming which allows the creation of new burgers using lists of ingredients, and computes the cost. A list of ingredients is included below, which you may add to as needed:

Ingredient List

Name | Price
---|---
Bun | 0.50
Ciabatta | 0.70
English Muffin | 0.70
Patty | 1.00
Feta | 0.25
Cheddar	| 0.20
Mozzarella | 0.20
Cheese Curds | 2.00
Egg | 0.20
Mayo | 0.05
Mustard | 0.03
Sour Cream | 0.03
Sriracha | 0.03
Arugula | 0.25
Black Garlic | 0.25
Chives | 0.05
Iceburg | 0.12
Pickle | 0.03
Spinach | 0.20
Red Onion | 0.01
Ritz Crackers | 0.25
Tomato | 0.07
Fries | 0.30
Gravy | 0.10


You are to test the correct usage of your application by assembling
the five test burgers are listed below: 

Name |  Ingredient List | Price
---|---|---
Never been Feta | Ciabatta, Patty, Feta, Mayo, Mustard, Arugula, Fries |2.58
Eggers can't be Cheesers|English Muffin, Patty, Cheddar, Egg, Iceburg, Fries|2.52
Poutine on the Ritz |  Bun, Patty, Iceburg, Tomato, Red Onion, Ritz Crackers, Fries, Gravy, Cheese Curds|4.35
Baby you can Chive my Car |  Bun, Patty, Feta, Mustard, Sour Cream, Chives, Pickle, Fries|2.19
Bet it all on Black Garlic |Bun, Patty, Mozzarella, Mayo, Sriracha, Black Garlic, Spinach, Fries|2.53

You should construct the burger, and verify through tests that the cost matches what is expected. 

You should also appropriately handle the case of attempting to use ingredients which do not exist.

Finally, add three of your own burgers, with names, ingredients and prices, and verify the correct cost is computed. You may add to the ingredient list as needed.

---

## Submission

Please submit your files on URCourses by the due date, in one `.zip` file. Include both the java files for the application, as well as the java files for the tests, in the appropriate directory. Finally, include the command line calls in a `readme.md` file for compiling and running tests for your application.

Example directory (zipped):
```
a-tilson-sid-ense375-l2.zip
├── readme.md
├── bin/
├── src/
│   ├── Burger.java
│   └── Ingredient.java
└── test/
    └── BurgerTest.java
```

Example commands in `readme.txt`:
```
javac -d bin ./src/Burger.java
javac -d bin ./src/Ingredient.java
javac -d bin -cp 'bin:junit-platform-console-standalone-1.7.0-all.jar' ./test/BurgerTest.java
java -jar junit-platform-console-standalone-1.7.0-all.jar -cp bin --scan-class-path
```
---

## References

JUnit5 Documentation, [JUnit Assertions](https://junit.org/junit5/docs/5.0.1/api/org/junit/jupiter/api/Assertions.html)

S. Bechtold, S. Brannen, J. Link, M. Merdes, M. Philipp, & C. Stein, [JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/), JUnit.org, 2018

M. Greencroft, [Practical Test-Driven Development for Java Programmers](https://www.lynda.com/Software-Development-tutorials/Practical-Test-Driven-Development-Java-Programmers/777389-2.html), Lynda from LinkedIn, 2018.

R. Osherove, [Naming standards for unit tests](https://osherove.com/blog/2005/4/3/naming-standards-for-unit-tests.html), Agile & XP Consulting and Training, 2005.
