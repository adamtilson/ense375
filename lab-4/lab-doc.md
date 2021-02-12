## ENSE 375 - Software Testing and Validation - Laboratory

# Lab 4: Introduction to DevOps

### University of Regina
### Faculty of Engineering and Applied Science - Software Systems Engineering

### Lab Instructor: [Adam Tilson](mailto:Adam.Tilson@uregina.ca)

---

## Development Silos

To understand DevOps, consider a traditional software development environment:

- A project has a number of different teams 
    - Management
    - Dev teams like front end, back end, data
    - Integrators
    - QA testers
- These teams work in "silos" - isolating code and knowledge from the other teams
- It's difficult for a developer to track their contributions to the bigger project, which is demotivating, they feel like 'cogs in the machine'
- Progress is bottlenecked - one group is often waiting on another
- Software spends weeks or months in each group before having a big release
- Finally, the project ends, the teams disband, and any knowledge that wasn't carefully documented is lost forever

Hopefully you can already see the problems with this method. This method has a lot of waste!
- Knowledge is wasted - People in one silo might have knowledge and code that benefits other groups
- Code is wasted - repetition between silos, extra coding due to unclear requirements
- Time is wasted - waiting on others

In the end a worse product is created, which your people needed to work harder to make.

## Introduction to DevOps (Development + Operations)

- DevOps is a multidisciplinary approach to software development which blends Software Engineering and Information Technology
    - A focus on collaboration between groups, including developers and testers
    - Open up the silos!
- Every person has the tools to learn about, commit, build, run and test all pieces of the project, easily and quickly.
    - Each person sees how their contribution fits into the bigger project, and if it breaks anything
- Promotes short / continuous development cycles leading to high quality software
- Improves the well-being of your developers, reducing burn out

- Supported by a toolchain which promotes:
    - planning 
    - issue tracking
    - communication and knowledge sharing
    - source control
    - continuous integration and continuous delivery
    - configuration management
    - cloud builds
    - logging and monitoring

## Introduction to Continuous Integration / Continuous Delivery (CI/CD)

Let's zoom in on one part of DevOps - CI/CD. These are the software development tools which facilitate opening these silos.
- CI/CD leverages automation technologies to simplify the process of integrating, building, testing and deploying.

Continuous Integration: 
- Previously, all of the teams push code to their own team repo, which is integrated by an "integrator". 
- Instead, we wish to have our devs take responsibility for this process
- Basically, we can 'always' get our working code into the main branch, effectively once or multiple times per day

Continuous Delivery: 
- Developers can build and test on any commit for any environment
- With instant feedback, developers can see if their commit has broken anything, and immediately begin to fix it
    - We may also wish to automatically compile and deploy builds to a live environment, 
    - e.g. a server, every time code is checked in. 
        - This is called Continuous Deployment, a special case of Continuous Delivery.
- Basically, we can 'always' get a release to build and test

### Agile Vs. CI/CD Vs. DevOps

These words clearly have overlapping meanings, with some subtle differences. 

My thoughts:
- `Agility` is what we want to do (processes for quick delivery, that are adaptable to change, and involve the client)
- `CI/CD` is how we are going to do it (build tools and automation)
- `DevOps` is why we do it (Culture, communication and responsiveness)

### A Practical Approach to CI/CD

1. Have one common mainline repo
   - use different branches for different teams

2. Commit frequently - one to three times per day
    - Every commit triggers a build
    - Every commit runs the automated tests
        - If the tests pass, merge changes into main
        - This could be done automatically on the server, e.g. using Jenkins
        - you get instant feedback if your commit broke anything so you can immediately fix it

3. Everyone sees everything 
    - what you are working on and how the tests are going is visible to all

## Build and Test Automation with Gradle

This lab will look at using the Build Automation tool, Gradle, as a first step in developing a CI/CD pipeline for Java. Unfortunately, developing a complete CI/CD pipeline exceeds the scope of this lab, as it would need to be tailored to every individual project and team, and likely use a dedicated development server.

In this lab we'll be running Gradle locally on our own machine.
- We'll learn about the building and testing automation without needing to dig into remote servers

Let's start by installing Gradle.

## Installation

You can [download Gradle releases here](https://gradle.org/releases/)

![Gradle DL][Gradle DL]

To install Gradle, simply unzip the archive, place it somewhere you can find it, and add the directory to `PATH` which contains: `gradle-6.8.2/bin`

If you want to read the docs, [they are available here](https://docs.gradle.org/6.8.2/userguide/userguide.html)

Note: You need to already have Java installed, which I will assume you do, because we've been using it for the past 3 labs!

## What can we do with Gradle?

- Create `Groovy` scripts which enable...
    - Automatically collecting dependencies from a central repository
    - Automatically building our application
        - Only if source files have changed
    - Automatically testing our application

## Sample Project with a dependency and test - No Gradle

Let's create a sample project which uses a dependency.

Let's build an application which requires JSON.simple, which we can grab from the [Maven Repository:](https://mvnrepository.com/artifact/com.googlecode.json-simple/json-simple/1.1.1)

We can download the `.jar` here:

![Simple JSON DL][Simple JSON DL]

You can find this project in the `eg-1` folder.

In this example, we use JSON.simple to turn the fields of an object into a JSON encoded string. We have a series of tests which check to see that each of the fields got encoded properly.

When you try to compile and run this code, you can see that there are now multiple dependencies, which makes the command line calls very long. Let's try to use Gradle to fix this.

## Sample Project - With Gradle

Let's start by adding a new file in our directory called `build.gradle`

This file is written in a programming language called `Groovy`, which runs on the JVM, and shows gradle how to build our file.

### Hello World and Syntax

Let's start with a gradle hello world, using Java syntax:

```
System.out.println("Hello World!")
```

and run it with...

```
gradle build
```

```
> gradle build

> Configure project :
Hello World!
```

Cool, but we want to do more. First, let's see what else we can do...

```
println("Hello World!")
```

Still works. So we don't need to import common Java libraries. What else...

```
println "Hello World!"
```

Still works. Parenthesis are optional in function calls when it is unambiguous.

Finally, we can switch our quotations:

```
println 'Hello World!'
```

This shows how Groovy syntax is derived from Java, and may help to explain some of the differences between examples you'll find online.

### A useful Gradle file...

Let's make a useful Gradle file. We'll add the following sections to our `build.gradle`, explained as we go.

First, Gradle can build many types of projects, we will tell it to build a Java application

```groovy
apply plugin: 'java'
```

This syntax unfortunately doesn't map back to Java, as far as I can tell.

Next, we can say where we are getting our dependencies from. We'll use the Maven repository:

```groovy
repositories {
    mavenCentral()
}
```

After this, we want to tell gradle where to find our input and our output. We can do that with this block:

```groovy
sourceSets {
    main {
        java {
            srcDirs = ['src']
            outputDir = file('bin')
        }
        
    }
    test {
        java {
            srcDirs = ['test']
            outputDir = file('bin')
        }
       
    }
}
```

Recall, this notation sets multiple properties of an object at once. This is effectively the same as...

```groovy
sourceSets.main.java.srcDirs = ['src']
sourceSets.main.java.outputDir = file('bin')
sourceSets.test.java.srcDirs = ['test']
sourceSets.test.java.outputDir = file('bin')

```

We also need to tell gradle which dependencies we need for our two build tasks, `implementation`, which compiles the code, and `testImplementation`, which tests our code:

```groovy
dependencies { 
    implementation 'com.googlecode.json-simple:json-simple:1.1.1'
    
    testImplementation 'org.junit.jupiter:junit-jupiter'
    testImplementation (platform('org.junit:junit-bom:5.7.1'))
}
```

Recall, when you see a statement like:
```groovy
implementation 'com.googlecode.json-simple:json-simple:1.1.1'
```

This is actually a function call. Groovy lets you omit parenthesis if the result is unambiguous, equivalently:

```groovy
implementation("com.googlecode.json-simple:json-simple:1.1.1")
```

And recall also that quotations are interchangeable between `"` and `'`. 

And finally, we need to tell gradle how to test our code using JUnit Jupiter
```groovy
test {
	useJUnitPlatform()
	testLogging {
		events "passed", "skipped", "failed"
	}
}
```

I found these commands from [JUnit github](https://github.com/junit-team/junit5-samples/blob/r5.7.1/junit5-jupiter-starter-gradle/build.gradle), [Gradle output sets](https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/SourceSetOutput.html),

In this directory you can build with:

```
gradle build
```

Some other cool things:
[Check out your dependencies](https://docs.gradle.org/current/userguide/viewing_debugging_dependencies.html) using...

```
gradle -q dependencies
```

We can create `Javadoc` documentation using

```
gradle javadoc
```

To learn how to add some more info to your Javadocs, you can check out the [docs from oracle](https://www.oracle.com/ca-en/technical-resources/articles/java/javadoc-tool.html), or [a quick tutorial and example](https://www.tutorialspoint.com/java/java_documentation.htm).

My suggestion is to add Javadocs only when you cannot write code which is self-descriptive.

## Integration Testing

While we do not typically test other libraries that we are using, we may want to write tests to ensure that we are using them properly. This is a component integration test, which is somewhere between unit tests and system integration tests.

## Assignment

This lab is intended to be open-ended to work with the varying demands of your schedule around midterm time. 

1. Select a Java library which provides some new functionality outside of typical Java.
2. Add it as a dependency using Gradle.
3. Write implementation code to utilize the library. 
4. Finally, some test cases to ensure that it is functioning as you intend. 
   - You could also write the test cases first, using TDD principles.

Your code and test's scope should be similar to eg-2, but using a different library.

Some examples of things you could attempt:
- Read and parse a certain type of file
- Scrape information from the web or use a web API
- Try a new programming feature, like Functional Programming

---

## Submission

Please include:
- Your source and test code
- Your build.gradle
- Any required supporting files, e.g. file types which your code reads

Do not include:
- Libraries. Gradle should fetch these for you automatically if correctly configured.

---

## References

J. Harmon, [Gradle for Java Developers](https://www.lynda.com/Developer-tutorials/Gradle-Java-Developers/585247-2.html), Lynda from LinkedIn, 2017.

B. Luijbregts, [Continuous Integration and Continuous Delivery: The Big Picture](https://app.pluralsight.com/library/courses/continuous-integration-delivery-big-picture), Pluralsight, 2017

R. Seroter, [DevOps: The Big Picture](https://app.pluralsight.com/library/courses/devops-big-picture/table-of-contents), Pluralsight, 2019

[Gradle DL]: res/gradle-dl.png "Gradle DL"
[Simple JSON DL]: res/simple-json-dl.png "Simple JSON DL"
