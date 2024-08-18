[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > Ctags/Cscope Cheat Sheet

# Ctags/Cscope Cheat Sheet



## Ctags

* CTags, short for "C tags," is a program that generates an index (or "tag") file of source code definitions in various programming languages.
* Primarily used for navigating and browsing large code-bases efficiently.
* The generated tag file contains information about identifiers, such as functions, classes, methods, variables, and macros, along with their locations within the source code.

### Install Ctags

* Install ctags using the following command:

  ```plain
  sudo apt install universl-ctags
  ```

### Generate Tags

* At the top-level of the source tree you want to navigate, run the following command:

  ```plain
  ctags -R *
  ```

### Using Ctags with Vim

* Commands

  | Command                   | Function                                                   |
  | ------------------------- | ---------------------------------------------------------- |
  | `Ctrl` + `]`              | Go to definition                                           |
  | `Ctrl` + `T`              | Return from the definicion                                 |
  | `Ctrl` + `W` `Ctrl` + `]` | Open the definition in a horizontal split                  |
  | :ts                       | List all of the definitions of the last tag                |
  | :ts `<tag_name>`          | List the tags that match `<tag_name>`                      |
  | :tn                       | Jump to the next matching tag                              |
  | :tp                       | Jump to the previous matching tag                          |
  | vi -t `<tag_name>`        | Go straight to the `<tag_name>` opening the file using Vim |
  
  > `Ctrl` + `]` - Probably the one you will use most often. It jumps to the definition of the tag (function name, structure name, variable name, or pretty much anything) under the cursor.
  >
  > `Ctrl` + `T` - Used to jump back up in the tag stack to the location you initiated the previous tag search from.
  >
  > :ts `<tag_name>` - Can be used to search for any tag, regardless of the file that is currently opened.
  >
  > :tn, :tp, :ts -  If there are multiple definitions/uses for a particular tag, the 'tn' and 'tp' commands can be used to scroll through them, and the 'ts' command can be used to 'search' a list for the definition you want (useful when there are dozens or hundreds of definitions for some commonly-used struct).



## Cscope

### Install Cscope

* Install cscope using the following command:

  ```tplain
  sudo apt install cscope
  ```

### Create Tag File

* At the top-level of the source tree you want to navigate, run the following command:

  ```plain
  find ./ -name "*.[chS]" > cscope.files
  ```

  > Find all file names that ends with `c`, `h` or `S` and store it in the file `cscope.files`.

  ```plain
  cscope -i cscope.files
  ```

* Check if the file `cscope.out` has been successfully generated

  ```plain
  ls cscope.out
  ```

### Using Cscope with Vim

* Open the cscope.out file using Vim and register `cscope.out` for source code navigation:

  ```plain
  vim cscope.out
  ```

  ```plain
  :cs add ./cscope.out
  ```
  
* Find the functions that "call" a given function or that "is called" by a given function.

  ```plain
  :cs find <command> <string>
  ```

  | Command | String                                               |
  | ------- | ---------------------------------------------------- |
  | s       | Find the C symbol `string`                           |
  | g       | Find the definition `string`                         |
  | d       | Find the functions called by the function `string`   |
  | c       | Find the functions which calls the function `string` |
  | t       | Find the text `string`                               |
  | e       | Find egrep pattern `string`                          |
  | f       | Find the file `string`                               |
  | i       | Find the files #include file `string`                |

### Basic Commands

* In command mode, type `cs find c memset` to list the path, filename and the line numbers of the occasions of `memset()`
* In command mode, type `cs find g main` to list the path, filename and the line number of the `main()` function.
* In command mode, type `cs find e shut*` to list the path, filename and the line numbers of the codes which include the string "shut*"



## Ctags vs. Cscope

* **Ctags** supports the following two features:
  1. Jump from function calls to their definitions
     * When you are over a call to a method, hitting `g]` or `Ctrl+]` will jump to the place where that method is defined or implemented. 
  2. Omni completion
     * When you type `foo.` or `foo->`, and if `foo` is a structure, then a pop-up menu with field completion will be shown.
* **Cscope** also supports jumping from function calls to their definitions but NOT omni completion. However cscope additionally adds the ability to jump to any of the places where a function is called as well.
* So as far as jumping around a code base is concerned, **ctags** will only ever lead you towards the place where the function is implemented,  whereas **cscope** can show you where a function is called too.
* Use both. **ctags** is  easier to set up, faster to run and if you only care about jumping one  way it will show you less lines. You can just run `:!ctags -R .` and `g]` just works. It also enables that omni complete thing.
* In conclusion, **ctags** is easier to set up and mostly works without doing much else, it's vital for omni-complete too. **Cscope** provides more  features if you have to maintain a large and mostly unknown code base,  but requires more leg work.
