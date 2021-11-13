# Programming 201 (Subsystem Design)

Goals: 
- Understanding of how subsystems & commands work
- Build a working, multi-command subsystem

Notes:
- Keep in mind that code on the RoboRIO runs as a loop with a CommandScheduler
- The loop executes every 20ms
- It is easy to have code that takes more than 20 seconds -- the solution is to schedule commands
- Command-based code inserts Commands that will be executed when the Scheduler gets to them. 
This avoids potentially overrunning the 20ms loop but executes almost instantaneously.

### Instructions
1. New project>Template>java>Command Robot
Name it Programming201 (in the git folder)
Investigate Robot.java (there isn't much code here)
- RobotContainer Class is instantiated
- There is a call to run() the CommandScheduler (in periodic)
- Also, calls an autonomous command in autonomousInit() and cancels the command in teleopInit(). This is a safety procedure to ensure the Auto command isn't running.
- Navigate from the auto command back through the RobotContainer to the ExampleCommand and the ExampleSubsystem to look them over.
- The RobotContainer should be where most of our logic goes.
- The ExampleSubsystem and ExampleCommand are just shells. They don't do anything except give examples about how they work.

2. What is a Subsystem?
- In programming we try to isolate systems so if one thing breaks everything else can keep working
- A Subsystem is a discrete interface to a set of I/O devices 
- Team 4786 will always build a Subsytem for each mechanism and will then create Commands to interact with those mechs. Examples: drive, intake, turret, shooter
- Today we will use the ExampleSubsystem to control a motor
- To do that we add a motor to the subsystem and add methods/functions in the Subsystem to interact with the motor

3. Updating the code

- WPILib>Manage Vendor Libraries>Install Online: https://www.revrobotics.com/content/sw/max/sdk/REVRobotics.json

- Edit the Contants
Constants are where we store variables that never change (like the IDs on motor controllers)
Then we can refer to these variables anywhere in our code.

Constants.java
```java
public final class Constants {
    public static Integer exampleMotorId = 4;
}
```
 
ExampleSubsystem.java
```java
import com.revrobotics.CANSparkMax;
import com.revrobotics.CANSparkMaxLowLevel.MotorType;

public class ExampleSubsystem extends SubsystemBase {
 
  final CANSparkMax exampleMotor;
  
  /** Creates a new ExampleSubsystem. */
  public ExampleSubsystem() {
    exampleMotor = new CANSparkMax(
	    Constants.exampleMotorId, MotorType.kBrushless);
  }
  
  public void go(double power){
    exampleMotor.set(power);
  }
}
```

4. Implementing the Subsystem
- Okay, so have a subsystem. But how does the robot know about our subsystem?
Ideally we can reference our ExampleSubsystem in a single spot where we can turn on/off this entire Subsystem and any Commands associated with it.

RobotContainer.java
```java
public class RobotContainer {
  // The robot's subsystems and commands are defined here...
  private final ExampleSubsystem m_exampleSubsystem = new ExampleSubsystem();
  ...
}
```
Now, throughout our code if we need access to the Subsystem we will reference RobotContainer.m_exampleSubsystem

5. What are Commands?
- Discrete events that can be scheduled
- The Command has 3 primary methods: init, execute, end
- is_finished() indicates if the Command should end automatically, or because some other event occurred.
- Examples from previous years: toggleIntake, targetShooter
- Some Commands are called over and over, like the drive commands (typically we call these OpenLoop) 
- Others are finite, like activating a Solenoid. The Command executes and is immediately finished

6. Making our ExampleCommand do something
This command will turn the motor on

ExampleCommand.java
```java 
  public void initialize() {
    m_subsystem.go(.3);
  }
```

7. Should be able to deploy at this point to a test bench
- Reminder that this command is defined in RobotContainer.java as the m_autoCommand
- Therefore when we run Autonomous the ExampleCommand will be executed.


8. Learn from each deployment
- How did the robot behave?
- Is that what you expected?
- The ExampleCommand was only scheduled once in autonomousInit() but it continued to run
- This is because the command's is_finished always returns false. So the command never stops and there is no code to stop the motor.

9. Make it better
- Let’s get the motor to stop after the motor has rotated a few times.
- Take a moment and think about how you might achieve this.

ExampleSubsystem.java
```java
public class ExampleSubsystem extends SubsystemBase {
  ...
  final CANEncoder exampleEncoder;
  ...
  
  public ExampleSubsystem() {
    ...
    exampleEncoder = exampleMotor.getEncoder();
    ...
  }
  
  ...
 
  public void stopMotor(){
    exampleMotor.set(0.0);
  }
 
  public double getTicks(){
    return exampleEncoder.getPosition();
  }
 
  public void resetTicks(){
    exampleEncoder.setPosition(0.0);
  }
 
}
```

ExampleCommand.java
```java
public class ExampleCommand extends CommandBase {
 
  // Called when the command is initially scheduled.
  @Override
  public void initialize() {
    m_subsystem.resetTicks();
    m_subsystem.go(.3);
  }
 
  // Called every time the scheduler runs while the command is scheduled.
  @Override
  public void execute() {
    SmartDashboard.putNumber("ticks", m_subsystem.getTicks());
  }
 
  // Called once the command ends or is interrupted.
  @Override
  public void end(boolean interrupted) {
    m_subsystem.stopMotor();
  }
 
  // Returns true when the command should end.
  @Override
  public boolean isFinished() {
    if (m_subsystem.getTicks() > 100){
      return true;
    } else {
      return false;
    }
  }
}
```


10. Control the exampleMotor with the Xbox controller
- I provide some assistance below but take a stab at solving this yourself
- You have to research and code a new command that will use a button to activate the motor
- It needs to happen during Teleop


### Example using Xbox buttons
Note: copied from ExampleCommand.java and renamed. Comments removed.

commands/ButtonCommand.java
```java
package frc.robot.commands;
 
import frc.robot.subsystems.ExampleSubsystem;
import edu.wpi.first.wpilibj2.command.CommandBase;
 
public class ButtonCommand extends CommandBase {
  @SuppressWarnings({"PMD.UnusedPrivateField", "PMD.SingularField"})
  private final ExampleSubsystem m_subsystem;
 
  public ButtonCommand(ExampleSubsystem subsystem) {
    m_subsystem = subsystem;
    addRequirements(subsystem);
  }
 
  @Override
  public void initialize() {
    m_subsystem.go(.3);
  }
 
 @Override
  public void execute() {}
 
  @Override
  public void end(boolean interrupted) {
    m_subsystem.stopMotor();
  }
 
  @Override
  public boolean isFinished() {
    return false;
  }
}
```
 
RobotContainer.java
```java
...
import frc.robot.commands.ButtonCommand;
import edu.wpi.first.wpilibj2.command.button.JoystickButton;
import edu.wpi.first.wpilibj.XboxController.Button;
...

public class RobotContainer {
  ...
  private final static XboxController xbox0 = new XboxController(0);
  ...
  private void configureButtonBindings() {
      new JoystickButton(xbox0, Button.kX.value).whileHeld(
          new ButtonCommand(m_intakeSubsystem)); 
  }
}
```
 
### Example using DefaultCommand (to use non-boolean buttons on controller)
Note: this was copied from ExampleCommand.java and modified. Comments removed.

commands/VariableCommand.java
```java
package frc.robot.commands;
 
import frc.robot.RobotContainer;
import frc.robot.subsystems.ExampleSubsystem;
import edu.wpi.first.wpilibj2.command.CommandBase;
 
public class VariableCommand extends CommandBase {
  @SuppressWarnings({"PMD.UnusedPrivateField", "PMD.SingularField"})
  private final ExampleSubsystem m_subsystem;
 
  public VariableCommand(ExampleSubsystem subsystem) {
    m_subsystem = subsystem;
    addRequirements(subsystem);
  }
 
  @Override
  public void initialize() {}
 
  @Override
  public void execute() {
    m_subsystem.go(RobotContainer.getIntakeSpeed());
  }
 
  @Override
  public void end(boolean interrupted) {
    m_subsystem.stopMotor();
  }
 
  @Override
  public boolean isFinished() {
    return false;
  }
}
```


RobotContainer.java
```
...
import edu.wpi.first.wpilibj.XboxController;
...

public class RobotContainer {
  ...
  private final static XboxController xbox0 = new XboxController(0);
  ...
 
    public RobotContainer() {
        configureButtonBindings();
        m_exampleSubsystem.setDefaultCommand(
            new VariableCommand(m_exampleSubsystem));
    }
 
    public static double getIntakeSpeed(){
        return xbox0.getY(GenericHID.Hand.kLeft);
    }
}
```

### Bonus Challenge
Sometimes balls get jammed. 
How could we tell if a ball is jammed to stop the controller from driving the motor? 
Sometimes the driver can’t see the robot so perhaps we can automate a routine to eject a “stuck” ball.

Proposed solutions:
- Could use the encoder and compare expected encoder ticks to voltage
- Could use a sensor to see that a ball is continually present in one position

