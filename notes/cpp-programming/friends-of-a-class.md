[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Friends of a Class

# Friends of a Class



## Friends of a Class

* Friend
  * A function or another class that has access to private class members
  * And, that function of or class is NOT a member of the class that it is accessing
  * Friends have unfettered access to both public and private data members of a class, but they're not members of the class.
* Friend functions
  * Can be regular non-member standalone functions
  * Can be member methods of another class
* Friend class
  * Another class can have access to private class members
  * The entire class will have access to the private information of the class granting friendship.
* Friendship must be granted, NOT taken!
  * A class must explicitly declare its friends in its class declaration using the `friend` keyword.
* Friendship is not symmetric
  * Must be explicitly granted
  * A being a friend of B does not necessarily make B a friend of A
* Friendship is not transitive
  * Must be explicitly granted
  * A being a friend of B, and B being a friend of C, does not necessarily make A a friend of C
* Friendship is NOT inherited



## Examples

* Non-member function as a friend

  ```cpp
  class Player
  {
      friend void display_player(Player &p);	// Non-member standalone function
      std::string name;
      int health;
      int xp;
  public:
      . . .
  };
  
  void display_player(Plyaer &p)
  {
      std::cout << p.name << std::endl;
      std::cout << p.health << std::endl;
      std::cout << p.xp << std::endl;
  }
  ```

  > The `Player` class grants the friendship.
  >
  > Since `display_player()` is a friend function of the class `Player`, it can modify the `Player`'s private data members. (It doesn't not have to use public getters/setters to access or modify the `Player`'s private data.)

* Member function of another class as a friend

  ```cpp
  class Player
  {
      friend void Other_class::display_player(Player &p);	// Member of another class
      std::string name;
      int health;
      int xp;
  public:
      . . .
  }
  
  class Other_class
  {
      ...
  public:
      void display_player(Player &p)
      {
          std::cout << p.name << std::endl;
          std::cout << p.health << std::endl;
          std::cout << p.xp << std::endl;
      }
  };
  ```

  > `display_player()` method of `Other_class` will now have access to everything in the `Plyaer` class.

* Another class as a friend

  ```cpp
  class Player
  {
      friend class Other_class;	// Another class
      std::string name;
      int health;
      int xp;
  public:
      . . .
  };
  ```
  
  > All the methods in the `Other_class` will have access to the `Player` class' private attributes.



## Note

* Sometimes we have classes that use other classes and writing code using getters/setters becomes long tedious and sometimes gives you overhead. However that shouldn't be the only reason for granting friendship. Friendships should be granted only when it makes sens in the design of your system, and then only the minimal necessary friendship should be granted.
