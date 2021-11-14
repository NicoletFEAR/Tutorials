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
- Power a motor
- Deploy code to Rio, control using FRC Driver’s Station
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

Robot.init
```java
public class Robot extends TimedRobot {
    ...
    private CANSparkMax motor1;
    ...
    public void robotInit() {
    	...
        motor1 = new CANSparkMax(4, MotorType.kBrushless);
    }
    ...
    
    public void autonomousPeriodic() {
      switch (m_autoSelected) {
        case kCustomAuto:
          // Put custom auto code here
          break;
        case kDefaultAuto:
        default:
          // Put default auto code here
	  motor1.go(.3);  // <-- this tells your new motor to run at 30%
          break;
      }
    }
}
```

3. Deploy and test
- If it didn’t work try to determine what is wrong.
- One thing that could be a problem is the ID of the motor controller

### ID the CANSparkMax
- Connect to a CANSparkMax motor controller using a USB-C cable
- On the laptop open RevSparkMax Client tool. Connect and set the ID to 4 (the same as we specified in the code)
- Re-deploy and test

4. Rewrite code we don't like
- There is a switch/case for the autonomousPeriodic that is complex
- Let's rewrite it as a simple if/then

Robot.init
```java
    public void autonomousPeriodic() {
      if (m_autoSelected == kCustomAuto) {
          // Put custom auto code here
      else if (m_autoSelected == kDefaultAuto) {
          // Put default auto code here
	  motor1.go(.3);  // <-- this tells your new motor to run at 30%
      }
    }
```
- Both ways work and do the same thing, but one is a bit more readable

### Save your code
- Programming with a team can be difficult. One person's changes may not match with another's.
- We need way to stay organized, know who made which changes etc.
- To do this we use git and the website github.com to store our code

1. In VSCode click the version control icon in the left navigation (three circles with some lines connecting them)
2. Initialize your repository and commit your changes with a brief message like "initial commit" (you might be prompted to save and/or stage your changes)
3. Committing confirms that you are happy with your changes, but they are only saved locally
4. To share your changes you must Push them to GitHub
5. On the laptop open the GitHub Desktop program
- File>Add local repository
- Navigate to the folder where your project lives: C:\Users\fearXX\git\Programming101\
- Repository>Push changes
- You might need to create a GitHub account to push your changes.
- After pushing your code is available to share with the world
- Go ahead and delete this project from the computer to clean up. The code can always be refetched from GitHub.
