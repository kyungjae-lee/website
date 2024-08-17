[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Embedded Systems Design using UML State Machines</a> > Events (Triggers)

# Events (Triggers)



## What is an Event?

* Events are incidents or a stimulus by which a state machine can be triggered. Incidents are abstracted as events.
* In state machine events cause transitions (external or internal)
* An event usually has 2 components
  * Signal
  * One or more associated values parameters (optional)



## Example

* Incidents in the operation of a microwave oven:
  1. Opening door (event) $\to$ Heater off, lights on
  2. Closing door (event) $\to$ lights off
  3. Set timer (event) $\to$ manage time
  4. Start (event) $\to$ heater ON
