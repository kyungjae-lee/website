[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Embedded Systems Design using UML State Machines</a> > Events (Triggers)

# Events (Triggers)



## What is an Event?

* Events are incidents or a stimulus by which a state machine can be triggered. Incidents are abstracted as events.

* In state machine events cause transitions (external or internal)

* An event usually has 2 components
  * Signal
  
    * e.g., TIME_CHANGE
  
      > This event signifies by its signal attribute that the user has pressed a button that changes time.
  
  * One or more associated values parameters (optional)
  
    * e.g., dir Direction_t
  
      ```c
      enum
      {
          UP,
          DOWN
      } Direction_t
      ```
  
      > This signal has an associated parameter that encodes whether the user has pressed a button that increases time (UP) or decreases time (DOWN).



## Example

* Incidents in the operation of a microwave oven:
  1. Opening door (event) $\to$ Heater off, lights on
  2. Closing door (event) $\to$ lights off
  3. Set timer (event) $\to$ manage time
  4. Start (event) $\to$ heater ON
