[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Using `const` & `static` with  Classes

# Using `const` & `static` with  Classes



## Using `const` with Classes

* You can pass arguments (e.g., pointers or reference) to class member methods as `const`, and they can't be modified while in the methods.
* We can also create `const` objects - It's attributes cannot change.
* What happens if we call member functions on `const` objects?
* `const` - Correctness

### Examples

* `const` object

  ```cpp
  const Player villain {"Villain", 100, 55};
  
  void display_player_name(const Player &p)
  {
      cout << p.get_name() << endl;
  }
  
  // What happens when you call member methods on a const object?
  villain.set_name("Nice guy");					// ERROR
  std::cout << villain.get_name() << std::endl;	// ERROR
  display_player_name(villain);					// ERROR
  ```

  > Once an object is created with `const` qualifier, its members cannot be modified.
  >
  > But, what's up with the `get_name()` function? Why is the compiler giving an error when the function is simply returning the name without modifying it?
  >
  > $\to$ It's because the compiler assumes that the `get_name()` function could potentially change the object!
  >
  > To solve this issue, you need to declare a function as `const` to assure the compiler that the function will not modify the object and therefore it is safe to call.

* `const` methods

  ```cpp
  class Player
  {
  private:
      . . .
  public:
      std::string get_name() const;
      // Compiler will generate an error if code in get_name modifies this object
      . . .
  };
  
  const Player villain {"Villain", 100, 55};
  villain.set_name("Nice guy");					// ERROR
  std::cout << villain.get_name() << std::endl;	// OK
  ```
  
  > Now the compiler will not only allow this method to be called on `const` objects, it will also produce a compiler error if you try to modify any of the object attributes in the method.
  >
  > Check out the concept of "Const-Correctness".



## `static` Class Members

* When we declare class data members as `static`, we're telling the C++ compiler that these data members belong to the class, not to any specific object.
  * Useful to create and store class-wide information
  
    e.g., How many player objects do we have active in our application at any point in time? $\to$ Create a `static` variable as a member of the `Player` class and then increment it and decrement it in the code whenever we create an object or delete an object.
  
* Class functions can be declared as `static`
  * Independent of any objects
  * Can be called using the class name
  * Since `static`, they only have access to static data members. They do not have access to non-static class data members.

### Examples

* `Player` class -  `static` members

  ```cpp
  /* Player.h */
  
  class Player
  {
  private:
      static int num_players;		// Cannot be initialized here
  
  public:
      static int get_num_players();	
      . . .
  }
  ```

  Initializing the `static` data

  ```cpp
  /* Player.cpp */
  
  #include "Player.h"
  
  int Palyer::num_players = 0;
  ```

  > L5: Initializing a `static` member variable must happen EXACTLY once, and it typically happens in the `.cpp` file for the class.

  Implementing `static` method

  ```cpp
  int Player::get_num_players()
  {
      return num_players;
  }
  ```

  > Note that `static` member methods only have access to `static` data members. They do not have access to non-static class data members.

* `Player` class - Update the constructor and destructor

  ```cpp
  Player::Player(std::string name_val, int health_val, int xp_val)
      : name{name_val}, health{health_val}, xp{xp_val}
  {
      ++num_players;
  }
  
  Player::~Player()
  {
      --num_players;
  }
  ```
  
  > Be careful! If you have many overloaded constructors since you might have to increment `num_players` in more than one place. Basically, anywhere you create an object. 
  >
  > We only have one constructor in the `Plyaer` class and we're delegating to it from the copy constructor. So this is the only place we want to increment `num_players` in this example.
  
* `main.cpp`

  ```cpp
  /* main.cpp */
  
  void display_active_players()
  {
      cout << "Active players: " << Player::get_num_players() << endl;
  }
  
  int main(void)
  {
      display_active_players();	// 0
      
      Player obj1{"Jack"};
      display_active_players();	// 1
      . . .
  }
  ```
