# Programming 202 - Applying PID

## What is PID?
PID refers to the proportional, integral, and derivitive tuning of values in a controller

### Thermostat example
When thinking of PID, a good example is a thermostat. Assume you are on vacation and the house has cooled to 18 degrees Celsius. 
When you arrive home your set the thermostat back to 22 degrees Celsius.
- The goal of the thermostat (our controller) is to reach the desired temperature.
- However, there is an inherent latency between the furnace and the air temperature.
It might take 5 minutes for heated air from the furnace to reach the thermostat.
- If the furnace runs at 100% until the thermostat detects 22 then shuts off (0%), the temperature will continue to rise.
- Then as the room cools again and the thermostat detects 21 degrees, it might cool to 20 degrees before the heated air reaches the thermostat again.
- This system of overshoot/undershoot is what PID is designed to fix

### FRC Elevator example
A common example in FRC is using a motor to drive a mechanism to achieve setpoints.
- Let's say you have an elevator mechanism that is driven by a chain.
- You use the motor to rotate a gear that causes the chain to turn and thereby the elevator to rise/fall.
- If you want the elevator to reach a specific height, how many times do you turn the motor?

- P (proportional) refers to the proportional difference between your current position and the setpoint.
If it takes 40 rotations of the motor to reach setpoint A, and you are at 0 rotations, then your Error (the difference between target and current positions) is 40.
The P variable decreases proportionally as you get closer to your setpoint. 
We would use the P variable to decrease/increase the speed of the motor to get to the setpoint as quickly as possible.

- I (integral) refers to the amount of Error over time.

