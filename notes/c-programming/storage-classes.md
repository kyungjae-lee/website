[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C Programming</a> > Storage Classes

# Storage Classes



## Disclaimer

* Storage classes can be specified for variables and - to a lesser extent - functions and parameters. We'll concentrate on variables for now.
* The term **block** refers to the body of a function (the part enclosed in braces) or a compound statement, possibly containing declarations.



## Properties of Variables

Every variable in a C/C++ program has three properties:

* Storage duration
* Scope
* Linkage

### 1. Storage Duration

Determines when memory is set aside for the variable and when that memory is released.

* **Automatic storage duration**

  Allocated when the surrounding block is executed

  Deallocated when the block terminates, causing the variable to lose its value.

* **Static storage duration**

  Stays at the same storage location as long as the program is running, allowing it to retain its value indefinitely.

### 2. Scope

The portion of the program text in which the variable can be referenced.

* **Block scope**

  The variable is visible from its point of declaration to the end of the closing block.

* **File scope**

  The variable is visible from its point of declaration to the end of the enclosing file.

### 3. Linkage

Determines the extent to which it can be shared by different parts of a program.

* **External linkage**

  May be shared by several (perhaps all) files (compilation units or source modules) in a program.

* **Internal linkage**

  Restricted to a single file, but may be shared by the functions in that file.

* **No linkage**

  Belongs to a single function and can't be shared at all.



By default storage duration, scope, and linkage of a variable depend on where it is declared.

* Variables declared *inside* a block (including a function body)
  * *automatic* storage duration
  * *block* scope
  * *no* linkage
* Variables declared *outside* any block, at the outermost level of a program
  * *static* storage duration
  * *file* scope
  * *external* linkage

* Example

  ```c
  int i;	// static storage, file scope, external linkage
  
  void f(void)
  {
      int j;	// automatic storage duration, block scope, no linkage
  }
  ```



For many variables, the default storage duration, scope, and linkage are satisfactory. When they aren't, we can alter these properties by specifying an explicit storage class: `auto`, `static`, `extern`, or `register`.



## Storage Classes in Detail

### The `auto` Storage Class

* Legal only for variables that belong to a block.
* An `auto` variable has:
  * automatic storage duration
  * block scope
  * no linkage
* Default for variables declared inside a block (so almost never specified explicitly)

### The `static` Storage Class

* Can be used with all variables, regardless of where they are declared

* The effect on a variable differs depending on where it is used:

  * *Outside* a block - `static` specifies that a variable has *internal* linkage

  * *Inside* a block - `static` changes the variable's storage duration from *automatic* to *static*

    ```c
    static int i;	// static storage, file scope, internal linkage
    
    void f(void)
    {
        static int j;	// static storage, block scope, no linkage
    }
    ```

  * When used in declaration outside a block, `static` essentially hides a variable within the file in which it's declared; only functions that appear in the same file can see the variable.

  * In the following example, the functions `f1` and `f2` both have access to `i`, but functions in other files don't:

    ```c
    static int i;
    
    void f1(void) { /* has access to i */ }
    void f2(void) { /* has access to i */ }
    ```

### The `extern` Storage Class

* Enables several source files to share the same variable

  ```c
  extern int i;
  ```

  > Informs the compiler that `i` is an `int` variable, but doesn't cause it to allocate memory for `i`
  >
  > Informs the compiler that we need access to a variable that's defined elsewhere

* A variable in an `extern` declaration has:

  * static storage duration

  * scope dependent on the declaration's placement:

    ```c
    extern int i;	// static storage duration, file scope, ? linkage
    
    void f(void)
    {
        external int j;	// static storage duration, block scope, ? linkage
    }
    ```

    > Determining the linkage of an `extern` variable is a bit harder.
    >
    > * If the variable was declared `static` in the file (outside of any function definition), then it has internal linkage.
    > * Otherwise (the normal case), the variable has external linkage.

### The `register` Storage Class

* Asks the compiler to store the variable in a register instead of keeping it in main memory like other variables.

  * This is a request to the compiler, not a command. It is the compiler's choice whether the variable will be stored in a register or in memory.

* Only legal for variables declared in a block

* A `register` variable has the same storage duration, scope and linkage as an `auto` variable.

  * One restriction is that since registers don't have addresses, it's illegal to use the `&` operator to take the address of a `register` variable. This restriction applies even if the compiler has elected to store the variable in memory.

* `register` is best used for variables that are accessed and/or updated frequently. (Why? Because it saves memory access time?)

  e.g., The loop control variable in a `for` statement is a good candidate for `register` treatment:

  ```c
  int sum_array(int a[], int n)
  {
      register int i;
      int sum = 0;
      
      for (i = 0; i < n; i++)
          sum += a[i];
      
      return sum;
  }
  ```

  

## The Storage Class of a Function

* Only options are `extern` and `static`

  * `extern` specifies that the function has external linkage, allowing it to be called from other files.
  * `static` indicates internal linkage, limiting use of the function's name to the file in which it's defined.
  * If no storage class is specified, the function is assumed to have external linkage.

* Examples:

  ```c
  extern int f(int i);	// f has external linkage
  static int g(int i);	// g has internal linkage
  int h(int i);			// h (by default) has external linkage
  ```

  > Because of its internal linkage, `g` can't be called directly from outside the file in which it's defined. (Declaring `g` to be `static` doesn't completely prevent it from being called in another file; an indirect call via a function pointer is still possible.)

* Declaring functions to be `static` has some benefits:

  * Easier maintenance
  * Reduced "name space pollution"

* Function parameters have the same properties as `auto` variables:

  * automatic storage duration
  * block scope
  * no linkage



## Summary

* The following program fragment shows all possible ways to include - or omit - storage classes in declarations of variables and parameters.

  ```c
  int a;
  extern int b;
  static int c;
  
  void f(int d, register int e)
  {
      auto int g;
      int h;
      static int i;
      extern int j;
      register int k;
  }
  ```

  ```plain
  Name  Storage Duration    Scope   Linkage
  ====  ==================  ======  ========
  a     static              file    external
  b     static              file    ?
  c     static              file    internal
  d     automatic           block   none
  e     automatic           block   none    
  g     automatic           block   none
  h     automatic           block   none
  i     static              block   none
  j     static              block   ?
  k     automatic           block   none
  ----  ------------------  ------  --------
  ```

  > ?: The definitions of `b` and `j` aren't shown, so it's not possible to determine the linkage of these variables. In most cases, the variables will be defined in another file and will have external linkage.
