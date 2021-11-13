# Programming 101

Goals
- basic understanding of the programming an FRC robot and comfort reading code

### Understand parts of robot
- Battery - 12V motorcycle battery
- Breaker - safely disconnect power from all devices
- PDP (Power Distribution Panel) - directs power to all devices
- Motor Controller - dictates when to send voltage to motor
- Electric Motor - many different types are available
- RoboRIO - onboard computer. It runs code that we write and tells devices how to behave
- CAN wiring - nervous system for hardware that identifies devices and communicates with them
- Laptop
- VSCode - programming IDE, gives hints about writing code correctly
- Java - the programming language Team 4786 uses
- WPILib - FRC libraries to make it easy to work with devices

### Getting acquainted with WPILib
- Click on WPI Logo - New project Template>java>Timed Robot
- Choose git folder at C:\Users\fearXX\git
- Name: Programming101
- Start exploring
- Robot code is in: src/main/java/frc/robot
- Robot.java is where the action begins
- Control+Click TimedRobot and appreciate how much is already done for you
- Understand that code is just a tool. You have to learn to use the tool and this is a complicated tool that takes time to understand
- Java is strongly typed. Ceremony around defining variables
- Object-oriented so we organize around objects

### Understanding what robots do
- Discuss init, periodic -- how code runs on Rio
- What should the robot do? What can it do? [ROTATE]
- Turn motors on/off
- Read sensors
- Control solenoids
- Send data/video back to driver’s station

### Today's activity
- Today we will drive a motor
- Deploy code to Rio, open Driver’s Station
- Auto chooser in SmartDashboard (what else might we show here?)
- Poke around, learn the tools

- Identify the motor in code

```java
	import com.revrobotics.CANSparkMax;
import com.revrobotics.CANSparkMaxLowLevel.MotorType;
```

- Why is it red and underlined?

WPI>Manage Vendor Deps, Install new libraries online
https://www.revrobotics.com/content/sw/max/sdk/REVRobotics.json

In class definition:
private CANSparkMax motor1;

```java
// robot.init:
motor1 = new CANSparkMax(4, MotorType.kBrushless);
```

In auto chooser:
	motor1.set(.3);

Deploy and test
Didn’t work? What is wrong?
Go back through code -- did we miss something?

Connect to Spark Max
Open Rev tool to ID motor
Re-deploy and test

Discuss switch/case, change to if/then. Which one is right?

Hard to program by committee. 
Need way to stay organized, know who made which changes etc.
We use git and the website github to store our code

In VSCode, initialize the repository and commit all files
In GitHub Desktop > Add Local Repository then publish to GitHub
