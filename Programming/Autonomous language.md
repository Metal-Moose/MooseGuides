# Autonomous language

This guide gives some insight into designing an autonomous language for your robot to use, why it is handy to have one (especially in games like Power-Up) and how to implement it.

## What is an autonomous language?

An autonomous language serves as abstraction above writing actual commands in code. Writing your autonomous in code tends to be quite slow, since you have to re-deploy every time you change something. 

Autonomous language helps you write Strings of words, that will then be parsed into the commands on the robot. This removes the need for re-deployment, as they can be passed into the code from the dashboard of your choice, and allows you to change autonomous on the fly (pretty much up until the start of autonomous). This is helpful, because being adaptive with autonomous was an important ability for a team to have during the 2018 season, due to the dynamic nature of the game.

## How could an autonomous language look like?

**Any way you want it to look!** 

Let me show you an example of a syntax that you might use, since it's one that's easy to implement. This is how our the syntax for our commands during the 2018 season looked like:

```
Function_1(parameter_1, parameter_2,... parameter_n)
```

A real example of a sequence of commands would then be something like `Drive(30.5) TurnBy(50) Outtake(0.5)`. The robot should drive 30.5 inches, turn by 50 degrees, and outtake for 0.5 seconds.

This is obviously only one of the ways your syntax could look like. You could replace the commas by dashes, round parentheses by square parentheses... it is really up to you, the concepts that we will be discussing stay the same regardless.

## Implementing a parser using [regular expressions](https://en.wikipedia.org/wiki/Regular_expression)

A regular expression (regex) helps you match parts of a String by defining a pattern to match. The pattern is defined using series of characters and symbols. I urge you to look up a tutorial or two, because otherwise the code snippets including regexes won't make much sense to you (for further information, look into **Resources** at the bottom of the guide).

Our first step is to split the String of commands into a String array of individual commands that we can work with. Let's start by developing a regular expression that does just that.

```java
/**
 * Parses a series of commands.
 *
 * @param commandString The commands to be parsed.
 */
static void parseCommand(String commandString) {
    // Splits the commandString into individual commands
    String[] commands = commandString.split(" ");
}
```

This is a start, but it has a few flaws, namely that it doesn't do even remotely what we wanted it to do. It splits the String at any space character, even between parameters of commands! Also, if there were two spaces, it would think that the String in between them (of length 0) was also a command.

To fix this, let's first trim the String (removes all leading and trailing spaces - this is also generally a good practice when trying to sanitize your inputs) and make it so that the regular expression matches the most spaces possible. We will also make it match the closed parentheses at the same time, since that determines where a command ends (note that this removes the closed parentheses, but we don't mind, since we would want to remove it later anyway).

```java
/**
 * Parses a series of commands.
 *
 * @param commandString The commands to be parsed.
 */
static void parseCommand(String commandString) {
    // Splits the commandString into individual commands
    String[] commands = commandString.split("\\) *");
}
```

This splits the String at points, where there is a **`)` character, follower by 0 or more spaces.** 

Now that we have our commands, let's iterate through them and deal with them individually! Let's try splitting them by a comma.

```java
/**
 * Parses a series of commands.
 *
 * @param commandString The commands to be parsed.
 */
static void parseCommand(String commandString) {
    // Splits the commandString into individual commands
    String[] commands = commandString.trim().split("\\) *");

    for (String command : commands) {
    	// Split the commands into their respective parts
        String[] commandParts = command.split(",");
    }
}
```

This doesn't really work well. Not only does it leave the whitespace around the parameters (for commands with more than 1 parameter), it doesn't even separate the first parameter from the name of the command! 

Let's improve it by making it split on either the comma and spaces, or the open parentheses and spaces.

```java
/**
 * Parses a series of commands.
 *
 * @param commandString The commands to be parsed.
 */
static void parseCommand(String commandString) {
    // Splits the commandString into individual commands
    String[] commands = commandString.trim().split("\\) *");

    for (String command : commands) {
    	// Split the commands into their respective parts
        String[] commandParts = command.split(" *\\( *| *, *");
    }
}
```

This works much nicer. We split when its **`(` with 0 or more spaces on either side, or `,` with 0 or more spaces on either side.** 

Now that we have the parts of our command, we can start deciding on what to do with them.

```java
/**
 * Parses a series of commands.
 *
 * @param commandString The commands to be parsed.
 */
static void parseCommand(String commandString) {
    // Splits the commandString into individual commands
    String[] commands = commandString.trim().split("\\) *");

    for (String command : commands) {
        // Split the commands into their respective parts
        String[] commandParts = command.split(" *\\( *| *, *");

        switch (commandParts[0]) {
            case "Drive":
                double distance = Double.parseDouble(commandParts[1]);

                break;
            case "TurnBy":
                double angle = Double.parseDouble(commandParts[1]);

                break;
            case "Outtake": 
                double time = Double.parseDouble(commandParts[1]);
                
                break;
        }
    }
}
```

Note that we could easily create something like `double parameterValue` to prevent the repeated `Double.parseDouble` calls in our code, the problem with this is that if you wanted to have commands without parameters, this would cause problems. There are a few ways around this, like creating a class holding data about the syntax of your language to be automatically parsed, but there was no need for this for our team, although it would definitely be a neater way to solve this issue.

We now know how to correctly parse all the values of the Strings, we're now ready to incorporate our function into the robot code!

## Incorporating parsing into robot code
The easiest way to implement a language like this would be to create a CommandGroup class and having this be the constructor for the class, so when you create an object, it automatically parses the String into command objects inside the CommandGroup class.

```java
public class AutonomousCommandGroup extends CommandGroup {
	// Constructor for autonomous
    public AutonomousCommandGroup(String commandString) {
        // Splits the commandString into individual commands
        String[] commands = commandString.trim().split("\\) *");

        for (String command : commands) {
            // Split the commands into their respective parts
            String[] commandParts = command.split(" *\\( *| *, *");

            switch (commandParts[0]) {
                case "Drive":
                    double distance = Double.parseDouble(commandParts[1]);

                    addSequential(new DriveDistance(distance));

                    break;
                case "TurnBy":
                    double angle = Double.parseDouble(commandParts[1]);

                    addSequential(new TurnByAngle(distance));

                    break;
                case "Outtake":
                    double time = Double.parseDouble(commandParts[1]);
                    
                    addSequential(new Outtake(time));

                    break;
            }
        }
    }
}
```

*Note that you have to have your own DriveDistance, TurnByAngle and Outtake command classe to create objects from, this simply shows the approach.*

The last thing to fully implement the parsing would be to use the SmartDashboard of your choice to get the commands from, so you can adjust the values all the way up to when the autonomous period starts.

## Improvements to the language
Not what you know the general approach, there are many ways to improve the language. Few examples of what you can do include:
* **Optional parameters** - sometimes, you want to adjust a variable of the command that would otherwise be left to its default value (speed of driving, sequential / parallel). You can adjust the parser to take this into account. An example of the syntax allowing this would be `Drive(50, Speed=0.3, Mode=Parallel)`.
* **Inverting commands** - when mirroring (having your robot do the same thing on the other side of the field) a series of commands, manually changing the values of allc commands that turn your robot would be a pain. Instead, an example of what you can do (using the code that we developed in this guide) is to pass a `boolean reverse` into the constructor of the AutonomousCommandGroup class. Then, inside our switch for the case `TurnBy`, if `reverse` is `true`, multiply the values of the variable `angle` by `-1` (changes turning from left to right and vice versa).
* **Recursively parsing commands** - a neat concept is to store your basic command Strings in an array and create "higher commands" by combining them. You can create a new command called `Replace(n)` that replaces itself by the basic command (by looking at the nth index of the basic commands array.
* **More language sanitization** - it is harder to make a mistake in the names of the commands when only the initials matter. `Drive(50, Speed=0.5)` becomes `D(50, S=0.5)`. This is really simple, and can be done using `.replace("[a-z]", "")` (removes all lower-case letters from a String). This is just an example on how to make the inputs less prone to human error, and it's one that we used during our 2018 season.

Now that you have the basic understanding of the concepts of parsing language, experiment! The possibilities are endless.

## Resources
* [RegexOne.com](https://regexone.com/) is a nice tutorial about the basics and helps you parctice your "regex skills".
* [Factory minds' cheatsheet](https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285) is handy when looking up specific meanings of various regex symbols.
* [Regex101.com](https://regex101.com/) is hands down my absolute favorite for testing your newly created regular expressions.
* [Metal Moose's 2018 robot code](https://github.com/Metal-Moose/MetalMoose2018) features an [AutonomousCommandGroup](https://github.com/Metal-Moose/MetalMoose2018/blob/master/MetalMoose2018CommandBased/src/org/usfirst/frc/team1391/robot/commands/AutonomousCommandGroup.java) that is implemented in a very similar way to this one.
* [Metal Moose's Programming Overview](https://drive.google.com/file/d/1prEAt9fbYlkzANMdPi_Zu7v9fk1xWKYo/view?usp=sharing) provides some more information about the autonomous language used during the 2018 season.
