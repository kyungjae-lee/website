[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Enumerations

# Enumerations



## Overview

* What is an enumeration?
* Motivation
* Structure of an enumeration
* Types of enumerations
  * Unscoped enumeration
  * Scoped enumeration

* Enumerations in use



## What is an Enumeration?

* A user-defined type that models a set of constant integral values.
  * The days of the week (Mon, Tue, Wed, ...)
  * The months of the year (Jan, Feb, Mar, ...)
  * The suits in a deck of cards (Clubs, Hearts, Spades, Diamonds)
  * The values in a deck of cards (Ace, Two, Three, ...)
  * States of a system (Idle, Defense_Mode, Attack_Mode, ...)
  * The directions on a compass (North, South, East, West)



## Motivation

* Prior to enumerated types many algorithms contained unnamed numerical constants (a.k.a. "Magic numbers"). These constants are unique values with often no explanation as to how they are obtained. To make matters worse, these constants were often used as conditionals in control statements leaving one with no idea what an algorithm was doing. This often resulted in many algorithms suffering from low readability and high numbers of logic errors.

  Therefore, the two important motivations for introducing the concept of enumeration were
  
  * To improve readability
  * To improve the algorithm correctness
  
* Improving readability of the code

  ```cpp
  int state;
  std::cin >> state;
  
  if (state == 0)
      initiate(3);
  else if (state == 1)
      initiate(4);
  else if (state == 2)
      initiate(5);
  ```

  > Poor readability since the meaning of values used in the code is not self-explanatory.

  Above code snippet can be improved by using enumerated types.

  ```cpp
  enum State {Engin_Failure = 0, Inclement_Weather = 1, Nominal = 2};
  enum Sequence {Abort = 3, Hold = 4, Launch = 5};
  
  int user_input;
  std::cin >> user_input;
  State state = State(user_input);
  
  if (state == Engin_Failure)				// state = 0
      initiate(Abort);					// sequence = 3
  else if (state == Inclement_Weather)	// state = 1
      initiate(Hold);						// sequence = 4
  else if (state == Nominal)				// state = 2
      initiate(Launch);					// sequence 5
  ```

  > Improved readability!

* Improving algorithm correctness

  ```cpp
  int get_state()
  {
      return state_of_fridge;
  }
  
  int getState()
  {
      return state_of_rocket;
  }
  ```

  ```cpp
  int state = get_state();
      
   if (state == 0)
      initiate(3);
  else if (state == 1)
      initiate(4);
  else if (state == 2)
      initiate(5);   
  ```

  > In this scenario, even though you used the wrong state getter function, the code will still compile because both of the getter functions return `int` values. $\to$ We don't want the launch sequence to be determined by the state of the refrigerator.

  By using enumerated types, we can impose a type restriction on the `state` variable.
  
  ```cpp
  State state = get_state();
      
  if (state == Engin_Failure)
      initiate(Abort);
  else if (state == Inclement_Weather)
      initiate(Hold);
  else if (state == Nominal)
      initiate(Launch);
  ```
  
  > Now, the variable `state` can only be one of the three predefined values of the enumerated type `State`. If we try to assign a value to the `state` variable that is not one of these three values, the compiler will generate an error! This means that if the programmer happens to call the wrong state getter function, the program will not compile.



## Structure of an Enumeration

* Syntax

  ```plain
  enum-key enum-name : enumerator-type { };
  ```

  > * `enum-key` - Defines the beginning of the enumeration as well as the scope (Regardless of the type of enumeration, scoped or unscoped, an enumeration always begins with the `enum` keyword.)
  > * `enum-name` - Optional name of the enumeration
  > * `enumerator-type` - Enumerator type; can be omitted and the compiler will try to deduce it
  > * `{ }` - Enumerator list; list of enumerator definitions (names and integral values associated with them)

* Enumerator initialization

  Simplest form - no name, no fixed underlying type

  ```cpp
  enum {Red, Green, Blue};	// Implicit initialization
  //	   0     1     2
  ```

  > Unless specified otherwise, the compiler will assign 0 to the first element, and then assign the value of 'previous enumerator + 1' to all the subsequent elements. (Implicit initialization)

  ```cpp
  enum {Red = 1, Green = 2, Blue = 3};	// Explicit initialization
  ```

  ```cpp
  enum {Red = 1, Green, Blue};	// Explicit/Implicit initialization
  //               2     3
  ```

* Enumerator type

  | Integral Type      | Width in Bits |
  | ------------------ | ------------- |
  | int                | at least 16   |
  | unsigned int       | at least 16   |
  | long               | at least 32   |
  | unsigned long      | at least 32   |
  | long long          | at least 64   |
  | unsigned long long | at least 64   |

  If the underlying type of enumeration is not fixed, the compiler assigns the first integral type that's able to hold the enumeration's entire value range.

  ```cpp
  enum {Red, Green, Blue};				// Underlying type: int
  //	   0     1     2
  //    000   001   010
  
  enum {Red, Green, Blue = -32769};		// Underlying type: long
  //     0     1    -32769
  //    000   001   11111111111111110111111111111111
  ```

  In some cases, we may want to fix an enumeration's underlying type for the purpose of saving memory or increasing the accuracy of calculations involving numerator values.

  ```cpp
  enum : uint8_t {Red, Green, Blue};		// Underlying type: unsigned 8 bit int
  enum : long long {Red, Green, Blue};	// Underlying type: long long
  ```

* Enumeration name

  ```cpp
  enum {Red, Green, Blue};	// Anonymous; No type safety (Not possible to declare variables
  							// of that enumeration type.)
  
  int my_color;
  
  my_color = Green;	// Valid
  my_color = 4;		// Also valid
  ```
  
  > No type safety for anonymous enumeration!
  
  ```cpp
  enum Color {Red, Green, Blue};	// Named; Type safe
  
  Color my_color;		// It is assumed that 'my_color' can only have one of the three values;
  					// Red, Green or Blue.
  
  my_color = Green;	// Valid
  my_color = 4;		// Not valid
  ```
  
  > With the enumeration name `Color`, it is now type safe!

 

## Unscoped Enumerations

* Enumerations whose enumerators are NOT qualified and are visible throughout the entire scope in which they declared.

* Syntax

  ```plain
  enum enum-name : enumerator-type { };
  ```

  > * `enum` - Enum; Defines an unscoped enumeration

### Common Usage

* Using `if` and `switch` statements with unscoped enumerations

  ```cpp
  State state = get_state();
      
  if (state == Nominal)					// Accessed first
      initiate(Launch);
  else if (state == Inclement_Weather)	// Accessed second
      initiate(Hold);
  else if (state == Engine_Failure)		// Accessed last
      initiate(Abort);
  ```

  > This control structure may not be the best implementation. Consider a situation where the current state is `Engine_Failure` and the system needs to initiate `Abort` sequence ASAP. With this control structure, you'll need to go through each and every conditional until it finally reaches `Engine_Failure`.

  `switch` statements are by far the most popular control structure used with enumerations. (Better readability and access time than `if...else` statements in general.)

  ```cpp
  State state = get_state();
      
  switch (state)
  {
      case Engine_Failure:	// Equal access time
          initiate(Abort);
          break;
      case Inclement_Weather:	// Equal access time
          initiate(Hold);
          break;
      case Nominal:			// Equal access time
          initiate(Launch);
          break;
  }
  ```

  > Note that each label within a `switch` statement must be unique!

* Using `cin` with unscoped enumerations

  ```cpp
  enum State {Engine_Failure, Inclement_Weather, Nominal};
  
  State state;
  std::cin >> state;	// NOT allowed using standard extraction operator (This is because the
  					// extraction operator `>>` has no knowledge of what a `State` type 
  					// variable is or how to deal with it.
  ```

  One solution is to extract the user input value into a type that the extraction operator `>>` does know about, and then cast it to the `State` type variable.

  ```cpp
  enum State {Engine_Failure, Inclement_Weather, Nominal};	// Underlying type: int
                   0                   1           2
  std::underlying_type_t<State> user_input;					// Type: int
  std::cin >> user_input;										// User input = 3
  State state = State(user_input);							// state = 3
  ```

  > When we declare the type of temporary variable, in some cases, the underlying type of an enumeration might not be obvious. So we can use the class template `std::underlying_type_t` to deduce it. In this case, the underlying type of the `State` enumeration is integer, so the type of the `user_input` variable will also be integer.
  >
  > It's important to note that when we cast a variable to an enumeration types, the value of the variable does not change. This means that there's no guarantee that the casted enumeration type variable will have the value corresponding to the enumeration's enumerators. For example, if the user were to enter the value 3, it would be cast to the `State` type variable `state` with no issues. Obviously, this is not what we want since 3 does not correspond to any `State` enumerator.
  >
  > One way to workaround this situation is to implement our own checks to ensure that the variable we are casting has a value corresponding to one of the enumeration's enumerators. This is usually done by using the `switch` statement with the condition being the variable to be casted (e.g., `user_input` in this case), and the cases being each of the enumeration's enumerator.
  >
  > There can be many other ways to do the checks, but the key is that it is your responsibility to perform the checks!

  ```cpp
  enum State {Engine_Failure, Inclement_Weather, Nominal};	// Underlying type: int
                   0                   1           2
  std::underlying_type_t<State> user_input;					// Type: int
  std::cin >> user_input;										// User input = 3
  State state;
  switch (user_input)
  {
      case Engine_Failure:		state = State(user_input); break;
      case Inclement_Weather:		state = State(user_input); break;
      case Nominal:				state = State(user_input); break;
      default:					std::cout << "User input is not a valid state.";
  }
  ```

  Another solution is to overload the extraction operator `>>` to deal with the `State` type variables.

  ```cpp
  enum State {Engine_Failure, Inclement_Weather, Nominal};	// Underlying type: int
                   0                   1           2
  std::istream& operator>>(std::istream& is, State& state)
  {
  	std::underlying_typt_t<State> user_input;		// Type: int
      is >> user_input;
      switch (user_input)
      {
          case Engine_Failure:		state = State(user_input); break;
          case Inclement_Weather:		state = State(user_input); break;
          case Nominal:				state = State(user_input); break;
          default:					std::cout << "User input is not a valid state.";
      }
  	return is;	// Input stream is returned to the calling function
  }
  ```

  With the overloaded extraction operator, the following code becomes not only valid, but also more clean and readable.

  ```cpp
  State state;
  std::cin >> state;		// Valid with overloaded extraction operator
  ```

* Using `cout` with unscoped enumerations to display enumeration-type variables

  ```cpp
  enum State {Engine_Failure, Inclement_Weather, Nominal};
  State state = Engine_Failure;
  std::cout << state;		// Displays 0
  ```

  > In this case, the enumeration-type variable is implicitly converted to its underlying type, and its value is displayed. (Note that it's their value that gets displayed, not the enumerator's name! This makes sense, because an enumerator's name isn't a string, but is simply a name used to identify its value within the code.)

  Sometimes, it may be useful to display the name of the enumeration type variable. To accomplish this, we can use `switch` statement.

  ```cpp
  enum State {Engine_Failure, Inclement_Weather, Nominal};
  State state = Engine_Failure;
  switch (state)
  {
      case Engine_Failure:		std::cout << "Engine Failure"; break;
      case Inclement_Weather:		std::cout << "Inclement Weather"; break;
      case Nominal:				std::cout << "Nominal"; break;
      default:					std::cout << "Unknown";
  }	// Displays "Engine Failure"
  ```

  Another way to accomplish this is to overload the insertion operator `<<`.

  ```cpp
  enum State {Engine_Failure, Inclement_Weather, Nominal};
  
  std::ostream& operator<<(std::ostream& os, const State& state)
  {
      switch (state)
      {
          case Engine_Failure:		os << "Engine Failure";
          case Inclement_Weather:		os << "Inclement Weather";
          case Nominal:				os << "Nominal";
          default:					os << "Unknown";
      }
      return os;	// Output stream is returned to the calling function
  }
  ```

  > L3: Note that the second parameter type must be the `const` reference! 

  By overloading the insertion operator, we can write a clean and readable code as follows.

  ```cpp
  State state = Engine_Failure;
  std::cout << state;		// Displays "Engine Failure"
  ```

  

## Scoped Enumerations

* Enumerations whose enumerators are qualified, and therefore they are only visible by using the scope resolution operator `::` . (Everything you can do with unscoped enumerations, you can do with scoped enumerations.)

* Syntax

  ```plain
  enum class enum-name : enumerator-type { };
  ```

  > * `enum class` (or `enum struct`) - Defines a scoped enumeration


* Unscoped enumerations can sometimes cause issues that can only be resolved by using scoped enumerations.

  Example 1:

  ```cpp
  enum Whale {Blue, Beluga, Gray};
  enum Shark {Greatwhite, Gammerhead, Bull};
  
  if (Beluga == Hammerhead)
      std::cout << "A beluga whale is equivalent to a hammerhead shark.";
  ```

  > Saying that `Beluga` whale and `Hammerhead` shark are equivalent just because they represent the same value 1 does not make sense!

  Example 2: Name clashes!

  ```cpp
  enum Whale {Blue, Beluga, Gray};
  enum Shark {Greatwhite, Gammerhead, Bull, Blue};	// Error: Blue already defined
  ```

  > We don't want to have to worry about name clashes when we are naming enumerators.

  Scoped enumerations solve these issues!

* Using `if` and `switch` statements with scoped enumerations (Identical to that of unscoped, but with the additional requirement of specifying enumerator scopes)

  ```cpp
  enum class Whale {Blue, Beluga, Gray};
  Whale whale = Whale::Beluga;
  
  if (whale == Whale::Blue)
      std::cout << "Blue whale";
  else if (whale == Whale::Beluga)
      std::cout << "Beluga whale";
  else if (whale == Whale::Gray)
      std::cout << "Gray whale";
  ```

  ```cpp
  enum class Whale {Blue, Beluga, Gray};
  Whale whale = Whale::Beluga;
  
  switch (whale)
  {
      case Whale::Blue:
      	std::cout << "Blue whale";
          break;
      case Whale::Beluga:
      	std::cout << "Beluga whale";
          break;
      case Whale::Gray:
      	std::cout << "Gray whale";
          break;
  }
  ```

* Using scoped enumerator values

  Unlike the unscoped enumerations, compiler will not deduce and convert the enumerator values of scoped enumerations. (Scoped enumeration type variables are not implicitly convertable! Instead, we must explicitly cast the variable to its underlying type.)

  ```cpp
  enum class Item {Milk = 350, Bread = 250, Apple = 132};		// Underlying type: int
  
  int milk_code = Item::Milk;				// Error: Cannot conver Item to int
  int total = Item::Milk + Item::Bread;	// Error: No matching '+' operator
  std::cout << Item::Milk;				// Error: No matching '<<' operator
  ```

  ```cpp
  enum class Item {Milk = 350, Bread = 250, Apple = 132};		// Underlying type: int
  
  int milk_code = int(Item::Milk);					// milk_code = 350
  // Or
  int milk_code = static_cast<int>(Item::Milk);
  
  int total = int(Item::Milk) + int(Item::Bread);		// total = 600
  std::cout << underlying_type_t<Item>(Item::Milk);	// Displays 350
  ```

  > L3, L5: Only after appropriate type casting (there can be a few different styles of casting), can we use the value of scoped enumeration type variable.
