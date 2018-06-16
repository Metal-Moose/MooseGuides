# FRC with IntelliJ

This guide explains, how to configure your IntelliJ IDE to be able to develop code for the RoboRIO.

If you have any problems with the guide, make sure to let us know!

## Getting IntelliJ
The first step is to install [IntelliJ IDEA](https://www.jetbrains.com/idea/).

## Installing the FRC Plugin

After installing and launching IntelliJ, you should be presented with the default menu screen with a multitude of options.

Go to `Configure - Plugins - Browse Repositories` and after typing "FRC" into the search, bar, click the green `Install`.

After that is done, click `Restart IntelliJ` (sing `Ok` and confirming the restart).

## Configuring the FRC Plugin

To input your team number into the plugin, go to `Configure - Settings`. Type "FRC" into the search bar. After finding the plugin settings, type your team number into the appropriate data field and confirm your selection using `Apply`.


## Creating a project

After correctly configuring the plugin, close all dialog boxes besides the main menu and select `New Project - FRC Robot Project`. If the project is missing a JDK, click `New` and add the path to your JDK (chances are that it tried looking into `C:\Program Files` and didn't find anything, so your JDK is probably installed in `C:\Program Files (x86)`).

Check the `Create from template` box and select the template of your choice (and click `Next`). After that, name your project and don't forget to rename the base package to whichever you like. The plugin uses a `frc.team<teamNumber>` convention, it is up to you if you would like to follow it or not.


## Common Problems

### Libraries not seen
If it so happens that IntelliJ doesn't see the project libraries, you can manually link them by adding another module to your project.

Go to `File - Project Structure - Modules` and add another dependency by clicking the green + button on the right side of the window and selecting `JARs or directories`, then simply add the folder where the WPILib JAR files are stored on your machine. 

## Resources
* [The link to the FRC plugin](https://plugins.jetbrains.com/plugin/9405-frc) and the [plugin's GitLab page](https://gitlab.com/Javaru/frc-intellij-idea-plugin).