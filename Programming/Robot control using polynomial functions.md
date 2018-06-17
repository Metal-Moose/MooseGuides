# Robot control using polynomial functions

This guide goes into how to use custom polynomial functions in your robot code to control parts of the robot *(and why would you ever do such thing)*.

## Why a polynomial function?

There are various applications, where closed-loop systems like PID could be used, but are not ideal - take our 2018's elevator control as an example. In our design (and most of the designs of other teams), the elevator had a set range of values - it could only go as low or as high as the design allowed, never further (unlike the drivebase, for example). This made it so that using either motion profiling or PID would be a bit of an overkill. 

A simple way to control it would be to just say that above and below a certain value, slow the elevator by a constant, which would work (but not really be elegant).

Using a polynomial function would be an effective way to control the desired robot part it in any position that it is currently in (since the range was restricted), and it just seemed like an interesting concept that we wanted to try out for the season.

## Modeling a polynomial function

### 1/ Define values

To model a function, we first need to define the points that it goes through. If we, for example, wanted to start at 0.2 in the beginning (go slowly), reach 1 at 50% and slow down back to 0.2 at 100%, we would define the points like this: 
1. `[0, 0.2]` - start slow.
2. `[50, 1]` - going full speed. 
3. `[100, 0.2]` - end slow.

**For `n`  points, a unique polynomial function of degree `n-1` can be formed** (given that no two points share the same x value, and there is more than 1 point). 3 points will therefore allow us to create a **unique 2nd degree** polynomial function.

### 2/ [Polynomial Regression](https://en.wikipedia.org/wiki/Polynomial_regression)!

The method that we will be using is called the **polynomial regression**, which is really just a fancy term for the method used for getting a polynomial function from given points.

The simplest way to calculate the polynomial function is to use an [online calculator](http://www.xuru.org/rt/pr.asp) or [Excel](http://www.statisticshowto.com/excel-multiple-regression/) to do it for us, and input the coefficients into an array on the robot (which was quite enough for our team during the 2018 FRC season). 

If, for some reason, this is not enough for you and you want to dynamically generate the coefficients from points on the robot, you can write your own polynomial regression algorithm using the [Least Squares Method](https://neutrium.net/mathematics/least-squares-fitting-of-a-polynomial/) (link is a well-writen article explaining the math behind the method).

## Evaluate function values in code
Now that we have the coefficients, how do we actually calculate the values of the function quickly? 

With Java, we could use an approach like this:

```java
/**
 * Returns the y value of a polynomial at a certain x position.
 *
 * @param x            The x position to calculate the y value at.
 * @param coefficients The coefficients of the polynomial function.
 */
double getYValue(double x, double[] coefficients) {
    double y = 0;
    for (int i = 0; i < coefficients.length; i++)
    	y += coefficients[i] * Math.pow(x, coefficients.length - i - 1);
    
    return y;
}
```

This would achieve what we wanted, but it is certainly not elegant. 

Let's think about the polynomial algebraically and take `5x^3+3x^2-2x+1` as an example. We could try to take out `x`, and get `x(5x^2+3x^1-2)+1`. After taking x out inside the parentheses a few more times, we get the resulting `x(x(x(5)+3)-2)+1`.

Transforming this into an algorithm and starting with 5, all we are doing is multiplying it by x and then adding the next coefficient on our list, effectively removing the need to exponentiate, simplifying, and speeding up our code (it also looks pretty cool to the judges)!

```java
/**
 * Returns the y value of a polynomial at a certain x position.
 *
 * @param x            The x position to calculate the y value at.
 * @param coefficients The coefficients of the polynomial function.
 */
double getYValue(double x, double[] coefficients) {
    double y = 0;
    for (double coefficient : coefficients) y = y * x + coefficient;
    
    return y;
}
```

When testing a 5th degree polynomial for 100 000 y values, version with Math.pow calls took just around 50 ms, while the simplified version took just 5 ms (it's roughly 10 times faster!).

## Usage in FRC code
The simplest way to incorporate this method into your robot's code would be to create a setThrottledSpeed() function in the subsystem that you wanted to control, use the measuring device (like an encoder) as the x values, calculating the y value using the method described above, and setting the speed of the motor to y when going in the positive direction and -y if going in the negative direction. 

A sample Java code would look like this:

```java
/**
 * Set throttled speed of the subsystem.
 *
 * @param speed Speed to which to set the motor to (this will be throttled).
 */
public void setThrottledSpeed(double speed) {
    double x = encoder.getDistance();   // The position of the robot part
    double[] coefficients = new double[]{0.213, 0.04412, 0.3};

    double y = 0;   // Calculate the desired speed
    for (double coefficient : coefficients) y = y * x + coefficient;

    // Sets the motor to speed, scaled down by the y value of the function
    motor.set(speed * y);
}
```

A similar thing can be seen in [Metal Moose's 2018 code](https://github.com/Metal-Moose/MetalMoose2018), namely the [elevator subsystem](https://github.com/Metal-Moose/MetalMoose2018/blob/master/MetalMoose2018CommandBased/src/org/usfirst/frc/team1391/robot/subsystems/Elevator.java). 

When looking at  the code, note that we have 2 pairs of 4th degree functions - one is for slowing the elevator down when going down and the other is for slowing it down when going up, because the curves of the slowing down are different thanks to gravity.

## Other Resources
[Metal Moose's Programming Overview](https://drive.google.com/file/d/1prEAt9fbYlkzANMdPi_Zu7v9fk1xWKYo/view?usp=sharing) provides some more information about the polynomial functions used during the 2018 season.
