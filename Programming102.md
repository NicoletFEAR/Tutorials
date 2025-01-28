# Programming102

### Goals: 
- Understanding how human drivers interact with code
- Build a working drivebase
- Storing our code with Git

### Notes
- First we will import a template drivebase from WPILib
- Many drivebases are available to help jump start a new team
- We will take an example and build simple drivebase code
- First and foremost -- no shame in copy/pasting code. Everyone does it!

### Tutorial

1. Using the WPI Icon in VSCode
- New project>Example>Tank Drive
- Choose folder at C:\Users\fearXX\git
- Name: Programming102

2. Look around
- CTRL+Click on DifferentialDrive class in Robot.java
- Constructor takes motor controllers
- verify method does some error checking
- There is a multi-motor example at top (more similar to our typical robots)
- Note the arcadeDrive and tankDrive methods. THey are both based on DifferentialDrive but behave differently.
- Notice "deadband" (ctrl+click) to see what it is doing
- Notice max output logic (what is the purpose of this?)
- Someone put a lot of thought into this -- take advantage of this (and all those who came before you)

3. Documentation is available on the web:
- Google "WPILib" and then search for DifferentialDrive. There are lots of notes and examples to help you 

### Modifying their code
1. They gave us a start in robot_init (but we want to use an `XBoxController` instead of the joysticks)

It is generally a good idea to comment out lines of code you are replacing until you have yours working.
Commenting in Java is putting two forward slashes in front of your comment. Or you can highlight multiple lines and use CTRL+/ for auto-commenting.
```java
public void robotInit() {
    ...
    // m_leftStick = new Joystick(0);
    // m_rightStick = new Joystick(1);
    ...
}
```

Add in your `new XboxController`
```java
public class Robot extends TimedRobot {
  ...
  private static XboxController xbox0;
  ...
}

public void robotInit() {
    ...
    xbox0 = new XboxController(0);
    ...
}
```

You might notice a squiggly line underneath XboxController. That happens because there is an error in your code. 
VSCode is trying to help you identify the problem. Hover over the text that is squiggly underlined and read the prompt.

In this case we need to import the XboxController package.
Use CTRL+. and it will suggest an import.
This should add this line near the top of the file.
```java
import edu.wpi.first.wpilibj.XboxController;
```


2. Now use the `xbox0` instead of the `m_leftStick` and `m_rightStick`
- Note that if you simply type “xbox0.” VSCode will suggest methods that are supplied by the `XboxController` class.
- Both `Joystick.getX` and `xbox0.getX` return a float value (i.e. a double precision value between 0.0 and 1.0)
- The XboxController has many input devices. The buttons are all Boolean (either true or false). 
However, the trigger buttons and 2 joysticks on the XboxController supply a float value.

```java
public void teleopPeriodic() {
  ...
  SmartDashboard.putNumber("Left", xbox0.getLeftY();
  SmartDashboard.putNumber("Right", xbox0.getRightY();
  m_myRobot.tankDrive(
    xbox0.getLeftY(), 
    xbox0.getRightY()
    );
  ...
}
```

- Don't forget to import GenericHID and SmartDashboard.
- SmartDashboard supplies us with a way to view data during a match.
- We can add values that the driver might want to see or even buttons to control the robot.
- In this case we are just printing some debugging values to view the output from the Xbox joysticks. 
After you deploy your code, make sure you check SmartDashboard to see what it is printing out.

3. Configuring the motor controllers
- We don't typically use a `PWMVictor`
- PWM is a way we can control the motors but we generally use CAN.
- To use CAN we use identify each motor with a unique ID (using Phoenix Tuner to get/set IDs of CTRE products and RevRobotics Software to get/set ids of SparkMax)
- The RobotRIO offers many input/output interfaces:
  - Digital (DIO): One or Zero, On or Off
  - PWM: pulse between On and Off very fast
  - Analog: a continuous range of values
  - Accelerometer: Data from an Accelerometer
  - I2C: Devises controlled with [I2C](https://en.wikipedia.org/wiki/I%C2%B2C)
  - CAN
- We need to pick a library that interacts with our controllers
- Add a Vendor Dep by clicking the WPI Icon and choosing Manage Vendor Libraries
Use the Install Libraries Online option and then paste in this Vendor Dep url (these can be found by searching online)
- This will install a set of libraries that allow you to connect to motor controllers made by CTRE or RevRobotics.

4. If you are using a VictorSPX
Vendor Dep: https://maven.ctr-electronics.com/release/com/ctre/phoenix/Phoenix-frc2022-latest.json


- Comment out the old m_myRobot and use this instead:
```java
public void robotInit() {
  ...
  m_myRobot = new DifferentialDrive(
      new WPI_VictorSPX(0), 
      new WPI_VictorSPX(1)
      );
  ...
}
```

4. If you are using a TalonSRX
Vendor Deps: https://maven.ctr-electronics.com/release/com/ctre/phoenix/Phoenix-frc2022-latest.json

- Comment out the old m_myRobot and use this instead:
```java
public void robotInit() {
  ...
  m_myRobot = new DifferentialDrive(
      new WPI_TalonSRX(0), 
      new WPI_TalonSRX(1)
      );
  ...
}
```

4. If you are using a SparkMax
Vendor Deps: https://www.revrobotics.com/content/sw/max/sdk/REVRobotics.json


- Comment out the old m_myRobot and use this instead:
```java
public void robotInit() {
  ...
  m_myRobot = new DifferentialDrive(
      new CANSparkMax(0, MotorType.kBrushless), 
      new CANSparkMax(1, MotorType.kBrushless)
      );
  ...
}
```

5. At this point you should be able to test your code!
- Open Driver Station
- Be Safe! When you first deploy new code you must be wary of things going wrong: use safety glasses, keep your distance, and start slow
- Select Teleop and Enable (after yelling out "Enabling" for all to hear).
- The motor controllers should display green lights when they are activated.
- You should be able to see the values output in SmartDashboard
- If you have motors attached they should rotate with a speed relational to how far you push the joystick from the center.


6. Switch to use the example provided in `DifferentitalDrive`
- Look at the example code found in `DifferentitalDrive`
```java
*/
public class Robot {
   SpeedController m_frontLeft = new PWMVictorSPX(1);
   SpeedController m_midLeft = new PWMVictorSPX(2);
   SpeedController m_rearLeft = new PWMVictorSPX(3);
   SpeedControllerGroup m_left = new  SpeedControllerGroup(m_frontLeft, m_midLeft, m_rearLeft);
 
   SpeedController m_frontRight = new PWMVictorSPX(4);
   SpeedController m_midRight = new PWMVictorSPX(5);
   SpeedController m_rearRight = new PWMVictorSPX(6);
   SpeedControllerGroup m_right = new SpeedControllerGroup(m_frontRight, m_midRight, m_rearRight);
 
   DifferentialDrive m_drive = new DifferentialDrive(m_left, m_right);
}
*/
```

- To make this work for you: change the Controllers and IDs for this to work. The IDs and controllers are based on our test-bench.
- If you don't have 6 motors you can't program for them. Referencing non-existent systems in your code is not allowed.
```java

public class Robot extends TimedRobot {
  ...
  SpeedController m_frontLeft = new WPI_VictorSPX(1);
  SpeedController m_rearLeft = new WPI_VictorSPX(15);
  SpeedControllerGroup m_left = new SpeedControllerGroup(m_frontLeft, m_rearLeft);
  SpeedController m_frontRight = new WPI_VictorSPX(0);
  SpeedController m_rearRight = new CANSparkMax(4, MotorType.kBrushless);
  SpeedControllerGroup m_right = new SpeedControllerGroup(m_frontRight, m_rearRight);
  DifferentialDrive m_myRobot = new DifferentialDrive(m_left, m_right);
  ...
}

public void robotInit() {
  // comment out the old m_myRobot
  ...
  // Deadband allows for a bit of drift in the joystick (i.e. the joystick doesn't do anything until you get to 10% of its throw)
  m_myRobot.setDeadband(.1);
  // MaxOutput is a good idea when testing. It puts a max limit on how much voltage to send to your motors
  m_myRobot.setMaxOutput(.3);
  ...
}

```

7. Team 4786 usuallys uses `ArcadeDrive`.

- These choices are generally made to suit the driver's taste.
- Try to figure out how to use `ArcadeDrive` on your own. Spoiler alert -- the answer is just below.

- Arcade Drive takes two variables: speed (forward/backward) and rotation (turn amount).
- In this example we use the joystick Y-axis for speed (forward and backward) and the X-axis for rotation (left and right).
- The arcadeDrive function converts these values into leftside/rightside voltage percentages
```java
m_myRobot.arcadeDrive(
      xbox0.getRightY(),
      xbox0.getRightX()
    );
```

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

### Conclusion
Drive is a simple example and one WPI gives us lots to work with (so any team can get on the field). 
The rest requires more creativity, research, and testing to make it work.

### Cleanup
- Commit your changes and Push your branch 
- Laptops charging
- Battery put away
- Cables where they go
- Joysticks where they go
- Keep the room neat!!

