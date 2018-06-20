# Java for beginners

This guide is an introduction to the Java language, and is recommended for anyone who doesn't have any (or much) prior experience with programming.

## Getting an IDE
An IDE (integrated development environment) is a really good way for you to start writing code. It helps you with syntax, points out a lot of the errors that you will be making, and generally makes your experience with programming a whole lot easier.

I would strongly recommend [IntelliJ](https://www.jetbrains.com/idea/), as it is a very solid choice for anyone looking to program in Java. There are other choices, like [NetBeans](https://netbeans.org/) or [Eclipse](https://www.eclipse.org/), although as a previous user of both, I'm not a huge fan of either.

After installing an IDE, create a Java project ([here is a tutorial for IntelliJ](https://www.jetbrains.com/help/idea/creating-and-running-your-first-java-application.html)) and proceed to the next chapter.

## Chapter 1: Hello, World!

After creating your project, your IDE should be showing code similar to this:

```java
package main;

public class Main {
	public static void main(String[] args) {

    }
}
```

You have no idea what this means, and it's absolutely fine!

For starters, let's try to write code that does something, because running the project as is wouldn't do much. 

Let's try to make the code print something. Add this line to your code and run the project:

```java
package main;

public class Main {
	public static void main(String[] args) {
		System.out.println("Hello, World!");
    }
}
```

It should print out the words in between the round brackets:

```console
Hello, World!
```

That wasn't hard, was it? You just wrote your very first program that did something! Although it isn't much, you will learn some powerful concepts in the upcoming chapters to better utilize what you just did.

## Chapter 2: Variables
An overwhelming majority of the programs that you will write will use variables, one way or another.

Variables are just a way to store information when your program is running. "Information" could mean a number, a word, or even a bunch of numbers!

This is what the syntax for creating a variable looks like:

```java
type name = value;
```

Let's look at what each of those mean to better understand, how it works:
* **type** is the kind of variable that we are creating - a number, a word...
* **name** is the name of the variable (how we'll refer to it).
* **value** is the value of the variable - 5, "Bob"...

There is a lot of types of variables, so let's look at a few basic ones:

| type    |          description          | value example |
|:-------:|:-----------------------------:|---------------|
| int     | A whole number.               | 5             |
| double  | A decimal number.             | 7.3           |
| boolean | A logical value - true/false. | true          |
| char    | A single character.           | 'a'           |

With that table in mind, let's try to create some variables and print them to the console!

```java
package main;

public class Main {
	public static void main(String[] args) {
		int myAge = 18;
        boolean iLikeChocolate = true;
        
        System.out.println("My age is: " + myAge);
        System.out.println("Do I like chocolate: " + iLikeChocolate);
    }
}
```

```console
My age is: 18
Do I like chocolate: true
```

Notice the `+` inside the round parentheses. You need to do this to combine words with other variable types.

A note to be made is that the way the program works is like a script for a drama club: it starts from the very top and reads to the bottom (as long as there's something to read). It first reads and remembers our variables `myAge` and `iLikeChocolate` and prints them out after that. If we were to do something like this:

```java
package main;

public class Main {
	public static void main(String[] args) {
        System.out.println("My age is: " + myAge);
        System.out.println("Do I like chocolate: " + iLikeChocolate);
    
		int myAge = 18;
        boolean iLikeChocolate = true;
    }
}
```

Either the IDE would start yelling at us, or the program would fail when running it, because it doesn't know what `myAge` is before we get to it...

This is not all that you can do with variables though. Let's try assigning one variable to another and see what happens:

```java
package main;

public class Main {
    public static void main(String[] args) {
        int myAge = 18;
        int myTwinsAge = myAge;

        System.out.println("I am " + myAge + " years old");
        System.out.println("My twin is " + myTwinsAge + " years old");
    }
}
```

To use correct terms for what we're doing: we first **declared** a variable `myAge` and **assigned** it the value 18. After that, we declared another variable called it `myTwinsAge` and assigned it the same value as `myAge`.

This is pretty nice, but we can't really do much else... that's where arithmetic operators come in!

### Arithmetic operators

Everybody knows the basic arithmetic operators from school (maybe besides the last one). This is how they look and work like in Java:

|  Operator Name | Symbol | Description               |  Example | Result |
|:--------------:|:------:|---------------------------|:--------:|--------|
| Addition       | `+`    | Adds two numbers.         | `1 + 2`  | `3`    |
| Subtraction    | `-`    | Subtracts two numbers     | `1 - 5`  | `-4`   |
| Division       | `/`    | Divides two numbers.      | `5 / 2`  | `2`    |
| Multiplication | `*`    | Multiplies two numbers.   | `9 * 4`  | `36`   |
| Modulo         | `%`    | Remainder after division. | `7 % 3`  | `1`    |

One thing to take note of is the result from the division operation. Since we're operating with integers, we can't have 2.5, because only whole numbers are allowed - that's why it automatically rounds down.

Let's see, how smart Java is, when it comes to order of operation.

```java
public class Main {
    public static void main(String[] args) {
        System.out.println(5 * 5 + 5);
        System.out.println(5 * (5 + 5));
    }
}
```
```console
30
50
```

Quite smart, it seems!

Not only did it correctly evaluate that multiplication comes first, we can also distinguish what comes first using round parentheses!

## Resources

### Writing the guide
* [Markdown Editor](https://jbt.github.io/markdown-editor/) from [James Taylor](https://github.com/jbt) is a great resource for when you need to see, how your MarkDown looks in real time.
* [Markdown Tables Generator](https://www.tablesgenerator.com/markdown_tables#) was quite helpful for correctly formating the tables.

### Java resources
* [Java: A Beginner's Guide](https://www.amazon.com/Java-Beginners-Seventh-Herbert-Schildt/dp/1259589315) is an absolutely stellar book and I would recommend it to anyone interested in learning Java.