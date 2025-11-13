# Programming 202 - Applying PID

## What is PID?
PID refers to the proportional, integral, and derivitive tuning of values in a controller.
This is also referred to as "closed loop" meaning that no additional feedback is necessary, the system is entirely automatic.

### Thermostat example
When thinking of PID, a good example is a thermostat. Assume you are on vacation and the house has cooled to 18 degrees Celsius. 
When you arrive home your set the thermostat back to 22 degrees Celsius.
- The goal of the thermostat (our controller) is to reach the desired temperature.
- However, there is an inherent latency between the furnace and the air temperature.
It might take 5 minutes for heated air from the furnace to reach the thermostat.
- If the furnace runs at 100% until the thermostat detects 22 then shuts off (0%), the temperature will continue to rise past the desired temperature.
- Then as the room cools again and the thermostat detects 21 degrees, the room might cool to 20 degrees before the heated air reaches the thermostat again.
- The problem of overshoot/undershoot is called oscillation and is what PID is designed to fix.

### FRC Elevator example
A common example in FRC is using a motor to drive a mechanism that we want to stop at determined setpoints.
- Let's say you have an elevator mechanism that is driven by a chain.
- You use the motor to rotate a gear that causes the chain to turn and thereby the elevator to rise/fall.
- If you want the elevator to reach a specific height and stop there, how many times do you turn the motor?
What if the elevator isn't perfectly balanced and descends due to gravity? 
How can we keep it at a specific point regardless of these variables?

- Equation: controllerOutput = func P(e) + func I(e) + func D(e)
Note: you don't really need to memorize this but just understand we are adding the three values together to determine the speed of the motor. 

- e (error) represents the difference between the target and the current position

- P (proportional) refers to the proportional difference between your current position and the setpoint.
If it takes 40 rotations of the motor to reach setpoint A, and you are at 0 rotations, then your Error is 40.
The P variable decreases proportionally as you get closer to your setpoint, i.e. the bigger the error, the bigger the command to correct it.
We can use the P variable to reduce the speed of the motor as we get closer to the setpoint. 
So if we are at 100% power with an Error of 40, how fast should we go once we reach 20 rotations?
We really want to slow the motor down quickly once we get to 38 rotations so we don't overshoot and then have to compensate.

- I (integral) refers to the amount of Error over time.
This is a compensation variable to P. 
Let's say you got to 40.5 rotations (of 40) and the P value got so low that the power to the motor no longer moves the elevator.
The "I" variable will increase over time so .5 error increases to 1 then 1.5 for each time interval. 
Eventually this I variable will be large enough to move the elevator. 
The main thing to remember is that I is in a hurry, if we don't get the error down to zero quickly enough the I will let you know about it.

- D (derivative) attempts to reduce the amount of overshoot
- Derivative is also based on time and looks at how quickly we are approaching the setpoint.
Let's say we set the speed of our motor too fast so that the P value isn't decreasing enough and we are going to blow past our setpoint.
The D variable sees how quickly we are approaching the setpoint and applies a brake so we land as close as possible on the first try.

### PID in practice
- There are lots of PID libraries out there. These are provided by WPILib and by the manufacturers of the motor controllers.
- So PID for us isn't so much math as it is tuning and setting variables correctly


### Understanding your mechanisms
- RevRobotics offers a lot of example code to checkout. See the entry in Resources below.
- This is a great example because it allows us to collect data about PID through testing.

1. WPILib>Create a New Project
- Template>java>Timed Robot
- Base folder: C:\Users\fearXX\git
- Name: PIDtesting
- Team: 4786

2. WPILib>Manage Vendor Libraries>Install new libraries (online)
https://www.revrobotics.com/content/sw/max/sdk/REVRobotics.json

3. Update the Robot.java file with this example taken from 

Robot.java
```java
/*----------------------------------------------------------------------------*/
/* Copyright (c) 2017-2018 FIRST. All Rights Reserved.                        */
/* Open Source Software - may be modified and shared by FRC teams. The code   */
/* must be accompanied by the FIRST BSD license file in the root directory of */
/* the project.                                                               */
/*----------------------------------------------------------------------------*/

package frc.robot;

import edu.wpi.first.wpilibj.TimedRobot;
import edu.wpi.first.wpilibj.smartdashboard.SmartDashboard;

import com.revrobotics.CANEncoder;
import com.revrobotics.CANPIDController;
import com.revrobotics.CANSparkMax;
import com.revrobotics.ControlType;
import com.revrobotics.CANSparkMaxLowLevel.MotorType;

public class Robot extends TimedRobot {
  private static final int deviceID = 4;
  private CANSparkMax m_motor;
  private CANPIDController m_pidController;
  private CANEncoder m_encoder;
  public double kP, kI, kD, kIz, kFF, kMaxOutput, kMinOutput;

  @Override
  public void robotInit() {
    // initialize motor
    m_motor = new CANSparkMax(deviceID, MotorType.kBrushless);

    /**
     * The restoreFactoryDefaults method can be used to reset the configuration parameters
     * in the SPARK MAX to their factory default state. If no argument is passed, these
     * parameters will not persist between power cycles
     */
    m_motor.restoreFactoryDefaults();

    /**
     * In order to use PID functionality for a controller, a CANPIDController object
     * is constructed by calling the getPIDController() method on an existing
     * CANSparkMax object
     */
    m_pidController = m_motor.getPIDController();

    // Encoder object created to display position values
    m_encoder = m_motor.getEncoder();

    // PID coefficients
    kP = 0.1; 
    kI = 1e-4;
    kD = 1; 
    kIz = 0; 
    kFF = 0; 
    kMaxOutput = 1; 
    kMinOutput = -1;

    // set PID coefficients
    m_pidController.setP(kP);
    m_pidController.setI(kI);
    m_pidController.setD(kD);
    m_pidController.setIZone(kIz);
    m_pidController.setFF(kFF);
    m_pidController.setOutputRange(kMinOutput, kMaxOutput);

    // display PID coefficients on SmartDashboard
    SmartDashboard.putNumber("P Gain", kP);
    SmartDashboard.putNumber("I Gain", kI);
    SmartDashboard.putNumber("D Gain", kD);
    SmartDashboard.putNumber("I Zone", kIz);
    SmartDashboard.putNumber("Feed Forward", kFF);
    SmartDashboard.putNumber("Max Output", kMaxOutput);
    SmartDashboard.putNumber("Min Output", kMinOutput);
    SmartDashboard.putNumber("Set Rotations", 0);
  }

  @Override
  public void teleopPeriodic() {
    // read PID coefficients from SmartDashboard
    double p = SmartDashboard.getNumber("P Gain", 0);
    double i = SmartDashboard.getNumber("I Gain", 0);
    double d = SmartDashboard.getNumber("D Gain", 0);
    double iz = SmartDashboard.getNumber("I Zone", 0);
    double ff = SmartDashboard.getNumber("Feed Forward", 0);
    double max = SmartDashboard.getNumber("Max Output", 0);
    double min = SmartDashboard.getNumber("Min Output", 0);
    double rotations = SmartDashboard.getNumber("Set Rotations", 0);

    // if PID coefficients on SmartDashboard have changed, write new values to controller
    if((p != kP)) { m_pidController.setP(p); kP = p; }
    if((i != kI)) { m_pidController.setI(i); kI = i; }
    if((d != kD)) { m_pidController.setD(d); kD = d; }
    if((iz != kIz)) { m_pidController.setIZone(iz); kIz = iz; }
    if((ff != kFF)) { m_pidController.setFF(ff); kFF = ff; }
    if((max != kMaxOutput) || (min != kMinOutput)) { 
      m_pidController.setOutputRange(min, max); 
      kMinOutput = min; kMaxOutput = max; 
    }

    /**
     * PIDController objects are commanded to a set point using the 
     * SetReference() method.
     * 
     * The first parameter is the value of the set point, whose units vary
     * depending on the control type set in the second parameter.
     * 
     * The second parameter is the control type can be set to one of four 
     * parameters:
     *  com.revrobotics.ControlType.kDutyCycle
     *  com.revrobotics.ControlType.kPosition
     *  com.revrobotics.ControlType.kVelocity
     *  com.revrobotics.ControlType.kVoltage
     */
    m_pidController.setReference(rotations, ControlType.kPosition);
    
    SmartDashboard.putNumber("SetPoint", rotations);
    SmartDashboard.putNumber("ProcessVariable", m_encoder.getPosition());
  }
}
```

4. Deploy the code to a test bench
- Make sure the motorID in code matches the one on your controller
- Set SmartDashboard "View>editable" and move the variables around so you can see them. Turn editable back off before Enabling.
- Adjust the variables in SmartDashboard to see how the PID is working.
- Generally the process is to set the P value first, then I, then D. 
- See if you can find variables that allow you to do 4 rotations and arrive at the desired location each time.
- Hint: running at 100% power might be a lot for 4 rotations


### Implementing PID in Commands
- Here is an example you can use to test a single motor and see how PID behaves
- We will use our newfound skills developing Subsystems to alter our existing ExampleSubsystem from Programming 201 to use PID

1. Git Clone your Branch of Programming201
2. Edit the ExampleSubsystem to use the CANSparkMax built-in PID.
Note that we have a new function that should allow us to specify a number of rotations

ExampleSusbsystem.java
```java
...
import com.revrobotics.CANPIDController;
...
public class ExampleSubsystem extends SubsystemBase {
  ...
  final CANPIDController examplePidController;
  public double kP, kI, kD, kIz, kFF, kMaxOutput, kMinOutput;
  
  public ExampleSubsystem() {
    
    exampleMotor = new CANSparkMax(
      Constants.exampleMotorId, MotorType.kBrushless);
    exampleEncoder = exampleMotor.getEncoder();
    exampleMotor.restoreFactoryDefaults();  // reset PID settings
    examplePidController = exampleMotor.getPIDController();

    // PID coefficients
    kP = 0.1; 
    kI = 1e-4;
    kD = 1; 
    kIz = 0; 
    kFF = 0; 
    kMaxOutput = .1; 
    kMinOutput = -.1;

    // set PID coefficients
    examplePidController.setP(kP);
    examplePidController.setI(kI);
    examplePidController.setD(kD);
    examplePidController.setIZone(kIz);
    examplePidController.setFF(kFF);
    examplePidController.setOutputRange(kMinOutput, kMaxOutput);

  }

  public void goRotations(double rotations){
    examplePidController.setReference(rotations, ControlType.kPosition);
  }
  ...
}  
```

3. Create two new Commands that will tell the motor to rotate 2 times and 4 times.
I will include only one of the commands -- you will need to create RotationsFour.java yourself.

commands/RotationsTwo.java
```java
package frc.robot.commands;
 
import frc.robot.subsystems.ExampleSubsystem;
import edu.wpi.first.wpilibj2.command.CommandBase;
 
public class RotationsTwo extends CommandBase {
  @SuppressWarnings({"PMD.UnusedPrivateField", "PMD.SingularField"})
  private final ExampleSubsystem m_subsystem;
 
  public RotationsTwo(ExampleSubsystem subsystem) {
    m_subsystem = subsystem;
    addRequirements(subsystem);
  }
 
  @Override
  public void initialize() {
    m_subsystem.goRotations(2.0);
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

4. Add buttons that will call our new functions in RobotContainer.
Note that the A button should rotate twice and the B button should rotate four times.

RobotContainer.java
```java
  ...
  private void configureButtonBindings() {
    new JoystickButton(xbox0, Button.kX.value).whileHeld(
        new ButtonCommand(m_exampleSubsystem)); 

    new JoystickButton(xbox0, Button.kA.value).whileHeld(
      new RotationsTwo(m_exampleSubsystem)); 

    new JoystickButton(xbox0, Button.kB.value).whileHeld(
      new RotationsFour(m_exampleSubsystem)); 
  }
  ...
```

5. Deploy your code and see how it behaves
- Do the other buttons still work?
- How does their behavior affect the PID commands?
- How could you compensate for the effect?

### Conclusion
- PID is relatively simple to implement, but challenging to get right
You should already have a pretty good idea what you are doing before implementing Commands etc.



### Resources
- WPILib PID libraries: 
https://docs.wpilib.org/en/stable/docs/software/advanced-controls/controllers/pidcontroller.html
There are simple-to-follow examples here that should work on any controller.

- Rev Robotics has a git repository that you can clone and try out their different PID models
https://github.com/REVrobotics/SPARK-MAX-Examples
Git clone the project then open any of the folders in the java directory in VSCode.
Each should be ready to deploy and provide an initial approach to different problems


