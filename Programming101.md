# Programming 101

### Goals
- basic understanding of programming an FRC robot
- comfort reading code

### Understand parts of robot
- RoboRIO - onboard computer. It runs code that we write and tells devices how to behave
- Motor Controller - dictates when to send voltage to motor
- Electric Motor - many different types are available
- CAN wiring - nervous system for hardware that identifies devices and communicates with them
- Laptop
- VSCode - programming IDE, gives hints about writing code correctly
- Java - the programming language Team 4786 uses
- WPILib - FRC libraries to make it easy to work with devices

### Getting acquainted with WPILib
- Open VSCode
- Click on WPI Logo (upper right) to open a menu
	- Create a New project
	- Select a project type > Template > Java > Timed Robot
	- Select a new project folder: "C:\Users\fearXX\git
	- Project Name: "Programming 101"
	- Team Number: 4786
- You should see a project on the left side
- Navigate into src/main/java/frc/robot
- Start exploring
- Robot.java is where the action begins
- Control+Click TimedRobot to open the library that is behind our project and appreciate how much is already done for you
- Code is just a tool. You have to learn to use the tool and this can a complicated tool that takes time to understand
- Java notes
	- Java is "strongly typed", this means that we have to declare what "type" each variable is (String, Double, Boolean)
	- Every statement must end in a semi-colon. That is how Java knows when you have completed your thought.
	- Variables can have qualifiers (final = won't change, static = same everywhere, public = available everywhere, private = only available to it's parent class) 
```java
private String my_variable = "Hello"; // You can put comments in to explain yourself
public Double rampRate = 4.6;  // Comments get ignored by Java so they don't need semicolons
private final CANSparkMax motor1;  // Create a variable that will be a reference to an electric motor

```

### Understanding what robots do
- Looking at the Robot.java file
	- robotInit - code that runs once, when the robot starts
	- robotPeriodic - code that runs every 20ms (50 times a second), when the robot is Enabled
	- autonomousInit - code that runs once, when Autonomous mode begins
	- autonomousPeriodic - code that runs every 20ms, while the robot is Enabled in Autonomous Mode
	- teleopInit - code that runs once, when the Teleop (Driver controlled) mode begins
	- teleopPeriodic - code that runs every 20ms, while the robot is Enabled in Teleop Mode
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
We often use CanSparkMax motor controllers so we will set one up now.

Robot.java (at the top where the other imports are)
```java
    import com.revrobotics.CANSparkMax;
    import com.revrobotics.CANSparkMaxLowLevel.MotorType;
```

- This shows up as red & underlined because it is an error. VSCode is letting you know something is wrong.
- The problem is that you referenced code libraries (com.revrobotics) that don't exist

2. Import necessary libraries

- WPI>Manage Vendor Deps>Install new libraries (online)
- Paste in this url to install the library: https://www.revrobotics.com/content/sw/max/sdk/REVRobotics.json

3. Set up and use a motor

Robot.init
```java
public class Robot extends TimedRobot {
    ...
    private final CANSparkMax motor1;
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
	  motor1.go(.3);  // <-- this tells your new motor to run at 30%
          break;
        case kDefaultAuto:
        default:
          // Put default auto code here
	  motor1.go(.1);  // <-- this tells your new motor to run at 10%
          break;
      }
    }
}
```

4. Deploy and test
	a. Power on the RoboRio
	b. Connect a USB cable from your laptop to the RoboRio
	c. Click the WPI logo in VSCode
	d. Type "deploy" in the box and choose: Deploy Robot Code
Note: This will deploy your code to the robot
- If it didn’t work try to determine what is wrong (read the error message in the console)
- One thing that could be a problem is the ID of the motor controller (see ID the CANSparkMax)	
	e. Open FRC Driver Station (the software that we use for controlling the robot)
	f. Make sure someone is holding the motor (not by the shaft)
	g. Choose "Autonomous" mode
	h. Click "Enable" to start the robot
	i. Click "Disable" to stop
	j. Go into ShuffleBoard/SmartDashboard and change the auto mode. "Enable" again and the motor should go faster.

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
          motor1.go(.3);  // <-- this tells your new motor to run at 30%
      else if (m_autoSelected == kDefaultAuto) {
	  motor1.go(.1);  // <-- this tells your new motor to run at 10%
      }
    }
```

### Clean up
- Close VSCode
- Delete this project from the computer C:\Users\fearXX\git\Programming101

### Contingencies

Victor Motor Controller
- Import Vendor Deps: http://devsite.ctr-electronics.com/maven/release/com/ctre/phoenix/Phoenix-latest.json
```java
private final WPI_VictorSPX motor1;
motor1 = new WPI_VictorSPX(4);  // <-- 4 means the ID of the motor controller
```

TalonSRX Motor Controller
- Import Vendor Deps: http://devsite.ctr-electronics.com/maven/release/com/ctre/phoenix/Phoenix-latest.json
```java
private final WPI_TalonSRX motor1;
motor1 = new WPI_TalonSRX(4);  // <-- 4 means the ID of the motor controller
```
