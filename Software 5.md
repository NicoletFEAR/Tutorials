# State Machine 101 - Getting Started

### Java and FRC concepts that you need to know
 - #### Methods
    - A method is a block of code, that when called, performs specific actions defined in the method
    - Is like a function in python or javascript
    - 2 main types of methods
    - Just completes an action
        - Looks like this
            ```java
              public void zeroGyroscope() {
                gyroscope.setYaw(0);
              }
            ```
        - void means that it doesnt return anything
        - zeroGyroscope is the name of the method
        - Parentheses are for parameters, for example if you wanted to be able to input a yaw
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
   - A java class is a template for an object
   - For example you would create a motor class that can drive a specific motor
   - You can create many instances of that class to drive different motors
   - This is an example of how you would declare a class called motor
        ```java
            public class motor {}
        ```
    - Inside the curly brackets is where you would put the contents of your class
 - #### Constructor
   - A constructor is used to take in parameters when constructing an object
   - This is an example of a constructor for a class called motor with the parameter deviceID which is an integer
        ```java 
            public motor (int deviceID) {}
        ```
   - Inside the curly brackets is where you would put code to use the deviceID to initialize a motor for example
 - #### Subsystems and Commands
    - Subsystems are game mechs/other robot systems
        - Motors
        - Sensors
    - There are 3 main types of subsystems
       - Voltage Controlled: Intakes and indexers
       - Velocity Controlled: Flywheels that need to be exact rpms
       - Position controlled: Arms, elevators, etc, anything that needs to be set to an exact position
    - Commands are the execution of game mech functions
        - Moving motors
        - Taking values from sensors and using them
 - #### Superclasses and Subclasses
    - If you look at any of our past code you will see that any class in the subsystem folder extends Subsystembase
        ```java
        public class PositionSubsystem extends SubsystemBase {}
        ```
    - In this example, SubsystemBase is the superclass, and PositionSubsystem is the subclass
    - Whenever you extend a class, you inherit all of the methods from that parent class
    - SubsystemBase has a method called periodic which runs every 20 ms allowing us to check a sensor every 20 ms for example
    - An abstract class is basically a template for other classes to use, while still having its own functionality
    - Abstract classes can't be initialized on their own
        ```java
        public abstract class PositionSubsystem extends SubsystemBase {}
        ```
- #### Enums
    - An enum is a special data type that consists of pre defined named values seperated by commans
        ```java
        public enum MechState {
            ON,
            OFF,
            SLOW,
            FAST
        }
        ```
    - You can add a constructor to it, similar to a class, to specify certain parameters that pertain to those values
        ```java
        public enum MechState {
            ON(100),
            OFF(0),
            SLOW(25),
            FAST(1000);
            
            public double position;
            private MechState(double position) {
                this.position = position;
            }
        }
        ```
    - The position variable is being set to what is put inside the parentheses
    - For example System.out.println(MechState.ON.position) would print out 100

### What is a State Machine
 - A state machine is a machine that has a number of states
 - This approach helps us interlock our mechs together more cleanly, and efficiently
 - On top of this, it makes it much easier to add more code which will be explained later

### Lets get started

 - #### Installation
     - Download the State Machine template folder from the github page and put it into your subsystem folder
     - Take a look at the files in the folder
     - Looks like a lot right?
     - Good thing that to implement this you don't need to understand all of it, you just need to know how to use it
     - However it's still good to have a basic understanding of whats going on behind the scenes
 - #### Understanding How It Works
     - Take a look at VoltageSubsystem.java
     - You will see things that are familiar
        - It's an abstract (Template) class
        - It has methods and a constructor
        - This is a template class for voltage controlled subsystems
    - Right click on VoltageSubsystemConstants in the constructor parameter and click Go To Defenition
      ```java
        protected VoltageSubsystem(final VoltageSubsystemConstants constants)
      ```
    - Here you will see all of the configuration for the voltage subsystem
    - When you create a new instance of a voltage subsytem, you will pass an instance of the constants to configure it
    - Scroll down farther and you will see methods to aid in the usage of this subsystem
    - Near the end you will see
        ```java
        public abstract void subsystemPeriodic();
    
        public abstract void outputTelemetry();
        ```
     - Similar to how an abstract class is a template class, an abstract method is a method that the subclass has to override and use
     - This is an example of how you would implement it in the subclass
        ```java
        @Override
        public void subsystemPeriodic() {//Put implementation here}
    
        @Override
        public void outputTelemetry() {//Put implementation here}
        ```
     - The last thing in the file is an interface called Voltage Subsystem State which is another template for an enum of all of the states that the subsystem can be
     - We see that it needs to inherit getName(); and getVoltage();
     - Lets look at an example implementation of this
        ```java
        public enum IntakeState implements VoltageSubsystemState {
            OFF(0, "Off"),
            IDLE(3, "Idle"),
            RUNNING(12, "Running");
        
            private double voltage;
            private String name;
        
            private LauncherFlywheelState(double voltage, String name) {
              this.voltage = voltage;
              this.name = name;
            }
    
            @Override
            public String getName() {
                return name;
            }
    
            @Override
            public double getVoltage() {
                return velocity;
            }
        }
        ```
     - You can see it overrides the inherited methods
 - #### What Happens With These States
    - Whenever setState() is called, it sets the voltage of the motor
    ```java
    public void setState(VoltageSubsystemState desiredState) {
        m_currentState = desiredState;
        m_master.setVoltage(m_currentState.getVoltage());
    }
    ```
 - This is the main idea of the state machine, instead of storing different position, velocity values in a constants file, its right there in the subsystem
 - Furthermore, you can just set the state to something that is easy to read like LauncherFlywheelState.ON
- #### How to configure the state machine?
  -   In the templates folder you will find SubsystemConstants.java
  -   Look at the different types of constants
  -   You see one for each type of subsystem, and one for a sparkmax
  -   In there you will see a template for how to define subsystem constants
  -   Heres an example of how you would define a VoltageSubsystemConstants
    ```java
      public static final SparkMaxConstants kWristIntakeMasterConstants = new SparkMaxConstants();
  
      static {
        kWristMasterConstants.kID = 45;
        kWristMasterConstants.kIdleMode = IdleMode.kBrake;
        kWristMasterConstants.kMotorType = MotorType.kBrushless;
        kWristMasterConstants.kCurrentLimit = 80;
        kWristMasterConstants.kInverted = false;
      }
  
      public static final SparkMaxConstants[] kWristIntakeSlaveConstants = new SparkMaxConstants[0];
  
      public static final VoltageSubsystemConstants kWristIntakeConstants =
          new IntakeSubsystemConstants();
  
      static {
        kWristIntakeConstants.kName = "WristIntake";
  
        kWristIntakeConstants.kSubsystemType = IntakeSubsystemType.WRIST_INTAKE;
  
        kWristIntakeConstants.kMasterConstants = kWristIntakeMasterConstants;
        kWristIntakeConstants.kSlaveConstants = kWristIntakeSlaveConstants;
  
        kWristIntakeConstants.kInitialState = WristIntakeState.IDLE;
      }
    }
  ```
  - We create a master smarkmax constants
  - And an empty slave constants array because theres only one motor
  - Then we define the main VoltageSubsystemConstants and define each variable in a static block
  - The static {} allows us to write code outside of a method or constructor
  - Now all you have to do is pass in these constants into your subsystems parameters
 
- #### Now we can create a subsystem
  -  Create a new file, lets say we name it IntakeSubsystem.java
      ```java
        public class WristIntake extends VoltageSubsystem
      ```
  - Make sure it extends voltage subsystem
  - Now create your constructor like this
    ```java
      public WristIntake(VoltageSubsystemConstants constants) {
        super(constants);
      }
    ```
  - Don't forget the abstract methods defined in the superclass that we need to implement
    ```java
      @Override
      public void subsystemPeriodic() {}
    
      @Override
      public void outputTelemetry() {}
    ```
 - Last but not least create an enum like shown above (Line 144) with all of the states you desire
 - Now your done!
 - Thats all it takes to create a subsystem state machine 


