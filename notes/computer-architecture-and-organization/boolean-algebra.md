[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Computer Architecture & Organization</a> > Boolean Algebra

# Boolean Algebra




## Boolean Algebra Summary

* **Boolean algebra** is an algebra with variables, the constants `0` and `1`, and the logical operations `AND`, `OR`, and inversion.
* Boolean algebra can be used to *simplify* a logical expression, and convert it into a different form with fewer terms.
    - Simpler expression means less logic, fewer parts, faster testing and greater reliability.
* Boolean algebra follows the laws of conventional algebra:
  - Closure
    - Results of operations performed on the set remain in the set.
  - Associative
    - Changing the order of the operation produces the same results.
    - e.g., `x + (y + z) = (x + y) + z
  - Commutative
    - Changing the order of the variable produces the same results.
    - e.g., xy = yx
  - Distributive
    - e.g., x(y + z) = xy + xz
  - Identity
    - Operations with the indentity do not change the value.
  - Inverse (Complement)
    - Operation with a variable and it's inverse produces the identity
      element.

* Except that in Boolean algebra we consider:
  - The set `S = {0, 1}`.
  - The operations **`+(OR)`** and **`·(AND)`**
  - The `+` identity element is `0`, and the `·` identity element is `1`.



## Nine Postulates of Boolean Algebra

* `+` is the logical `OR`, `· (dot)` is the logical `AND`.

  ```plain
  1. X + 0 = X    (ORing a variable with 0 has no effect on the variable)
  2. X + 1 = 1    (ORing a variable with 1 gives 1)
  3. X + X = X    (ORing a variable with itself has no effect on the variable)
  4. X + X` = 1   (ORing a variable and its complement is 1)
  5. X·1 = X      (ANDing a variable with 1 has no effect on the variable)
  6. X·0 = 0      (ANDing a variable with 1 gives 0)
  7. X·X = X      (ANDing a variable with itself has no effect on the variable)
  8. X·X` = 0     (ANDing a variable with a complement gives 0)
  9. X`` = X      (The complement of a complement cancels out)
  ```



## Properties of Boolean Variables

* Boolean variables follow the normal commutative, associative, and distributive laws of conventional algebra.

  ```plain
  X + Y + Z = Z + Y + X         (commutative law)
  X·Y = Y·X                     (commutative law)
  X + (Y + Z) = (X + Y) + Z     (associative law)
  X·(Y·Z) = (X·Y)·Z             (associative law)
  X + Y·Z = (X + Y)·(X + Z)     (distributive law)
  X·(Y + Z) = X·Y + X·Z         (distributive law)
  (X + Y)' = X'·Y'              (de Morgan's law)
  (X·Y)' = X' + Y'              (de Morgan's law)
  X + X'·Y = X + Y
  ```
