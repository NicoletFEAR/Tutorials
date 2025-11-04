# Programming 1 (Basics and Motor Control)

### Goals
- basic understanding of programming an FRC robot
- comfort reading code

### Understand parts of the robot
- RoboRIO - onboard computer. It runs code that we write and tells devices how to behave
- Motor Controller - dictates when to send voltage to the motor
- Electric Motor - many different types are available
- CAN wiring - nervous system for hardware that identifies devices and communicates with them
- Laptop
- VSCode - a programming IDE, gives hints about writing code correctly
- Java - the programming language Team 4786 uses
- WPILib - FRC libraries to make it easy to work with devices

### Important object-oriented concepts in Java
- ##### Methods
    - A method is a block of code that, when called, performs specific actions defined in the method
    - It's like a function in Python or JavaScript
    - 2 main types of methods
    - Just completes an action
        - Looks like this
            ```java
              public void zeroGyroscope() {
                gyroscope.setYaw(0);
              }
            ```
        - void means that it doesn’t return anything
        - zeroGyroscope is the name of the method
        - Parentheses are for parameters, for example, if you wanted to be able to input a yaw
            ```java
              public void setYaw(int yaw) {
                gyroscope.setYaw(yaw);
              }
            ```
    - Another type returns something
        - Looks like this
            ```java
              public int getYaw() {
                return gyroscope.getYaw();
              }
            ```
        - int is the return type
        - getYaw is the name

- #### Classes
   - A Java class is a template for an object
   - For example, you would create a motor class that can drive a specific motor
   - You can create many instances of that class to drive different motors
   - This is an example of how you would declare a class called motor
        ```java
            public class motor {}
        ```
    - Inside the curly brackets is where you would put the contents of your class

### Getting acquainted with WPILib
- Open VSCode
- Click on WPI Logo (upper right) to open a menu
	- Create a New project
	- Select a project type > Template > Java > Timed Robot
	- Select a new project folder: "C:\Users\fearXX\git
	- Project Name: "Programming 1"
	- Team Number: 4786
- You should see a project on the left side
- Navigate into src/main/java/frc/robot
- Start exploring
- Robot.java is where the action begins
- Files are named for the classes they contain, so Robot.java has the Robot class in it
- You can Control+Click TimedRobot to see the code that’s already been written for you
- Control+Clicking lets you see the source code (Where the code is defined)  for any class
- Code is just a tool. You have to learn to use the tool and this can a complicated tool that takes time to understand
- #### More Java notes
	- Java is "strongly typed," this means that we have to declare what "type" each variable is (String, Double, Boolean)
	- Every statement must end in a semi-colon. That is how Java knows when you have completed your thought.
	- Variables can have qualifiers. For example, public = available everywhere, while private = only available to other code in the same class)
```java
private String myVariable = "Hello";
public double rampRate = 4.6;
public boolean isFull = true;
private int bottomLeftMotorId = 22; // Those who know 22
```
- #### What is the difference between these variable types?
	- myVariable is a String (sequence of text) and private (only code in the same class can see it)
	- rampRate is a double (real number) and public (code in any class can see it)
	- isFull is a boolean (true or false value) and public
	- bottomLeftMotorId is an int (integer) and private

### Understanding what robots do
- Some methods in the Robot class
	- robotInit - code that runs once, when the robot starts
	- robotPeriodic - code that runs every 20 milliseconds (50 times a second), when the robot is Enabled
	- autonomousInit - code that runs once, when Autonomous mode begins
	- autonomousPeriodic - code that runs every 20 milliseconds, while the robot is Enabled in Autonomous Mode
	- teleopInit - code that runs once, when the Teleop (Driver controlled) mode begins
	- teleopPeriodic - code that runs every 20 milliseconds, while the robot is Enabled in Teleop Mode
- What can we do with programming on a robot?
	- Control motors -- turn them on/off or adjust speed by specifying an amount of power (voltage)
	- Control solenoids -- make a pistons go out or in
	- Read sensors -- encoders tell us how many times a motor has turned, ultrasonic tell us how far we are from something solid
	- Send data/video back to driver’s station
	- Read data from a camera to find targets and judge distance from targets

### Today's activity
- Power a motor
- Deploy code to RoboRio, control using FRC Driver’s Station
- Auto chooser in SmartDashboard
- Poke around, learn the tools

1. Identify the motor in code
	- We often use CanSparkMax motor controllers so we will set one up now.

Robot.java (at the top where the other imports are)
```java
    import com.revrobotics.CANSparkMax;
    import com.revrobotics.CANSparkMaxLowLevel.MotorType;
```

> :memo: **Note:** This shows up as red & underlined because it is an error. VSCode is letting you know something is wrong.<br>
> The problem is that you referenced code libraries (com.revrobotics) that don't exist

2. Import necessary libraries
	- WPI>Manage Vendor Deps>Install new libraries (online)
	- Paste in this url to install the library: https://www.revrobotics.com/content/sw/max/sdk/REVRobotics.json

3. Set up and use a motor(Copy and Paste the following code into Robot.init)

Robot.init
```java
public class Robot extends TimedRobot {
    ...
    private static CANSparkMax motor1;
    ...
    public void robotInit() {
    	...
        motor1 = new CANSparkMax(4, MotorType.kBrushless);  // <-- 4 means the ID of the motor controller
    }
    ...
    
    public void autonomousPeriodic() {
      switch (m_autoSelected) {
        case kCustomAuto:
          // Put custom auto code here
	  motor1.set(.3);  // <-- this tells your new motor to run at 30%
          break;
        case kDefaultAuto:
        default:
          // Put default auto code here
	  motor1.set(.1);  // <-- this tells your new motor to run at 10%
          break;
      }
    }
}
```

4. Deploy and test
	- Power on the RoboRio
	- Connect a USB cable from your laptop to the RoboRio
	- Click the WPI logo in VSCode
	- Type "deploy" in the box and choose: Deploy Robot Code
		> :memo: **Note:** This will deploy your code to the robot<br>
		> If it didn’t work try to determine what is wrong (read the error message in the console)<br>
		> One thing that could be a problem is the ID of the motor controller (see ID the CANSparkMax)<br>
	- Open FRC Driver Station (the software that we use for controlling the robot)
	- Make sure someone is holding the motor (not by the shaft)
	- Choose "Autonomous" mode
	- Click "Enable" to start the robot
	- Click "Disable" to stop
	- Go into ShuffleBoard/SmartDashboard and change the auto mode. "Enable" again and the motor should go faster.

### ID the CANSparkMax
- Connect to a CANSparkMax motor controller using a USB-C cable
- On the laptop open RevSparkMax Client tool. Connect and set the ID to 4 (the same as we specified in the code)
- Re-deploy and test


### Bonus Round
- Rewrite code we don't like
- There is a switch/case for the autonomousPeriodic that is overly complex
- Let's rewrite it as a simple if/else statement
- Both ways work and do the same thing, but one is a bit more readable

Robot.init
```java
    public void autonomousPeriodic() {
      if (m_autoSelected == kCustomAuto) {
          motor1.set(.3);  // <-- this tells your new motor to run at 30%
      else if (m_autoSelected == kDefaultAuto) {
	  motor1.set(.1);  // <-- this tells your new motor to run at 10%
      }
    }
```

### Clean up
- Close VSCode
- Delete this project from the computer C:\Users\fearXX\git\Programming1

### Contingencies

Victor Motor Controller
- Import Vendor Deps: https://maven.ctr-electronics.com/release/com/ctre/phoenix/Phoenix-frc2022-latest.json
```java
import com.ctre.phoenix.motorcontrol.can.WPI_VictorSPX;
private final WPI_VictorSPX motor1;
motor1 = new WPI_VictorSPX(4);  // <-- 4 means the ID of the motor controller
```

TalonSRX Motor Controller
- Import Vendor Deps: https://maven.ctr-electronics.com/release/com/ctre/phoenix/Phoenix-frc2022-latest.json
```java
import com.ctre.phoenix.motorcontrol.can.WPI_VictorSPX;
private final WPI_TalonSRX motor1;
motor1 = new WPI_TalonSRX(4);  // <-- 4 means the ID of the motor controller
```

