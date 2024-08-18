[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > Shell Script

# Shell Script



## Notations used in Shell Scripts

* Positional parameters: `$0`, `$1`, `$2`, ...

  * These variables hold the values of command-line arguments passed to the script when it is executed.

  * `$0`: Contains the name of the script or the command used to invoke the script. It represents the script's own name

  * `$1`, `$2`, `$3`... : Hold the values of the command-line arguments passed to the script. `$1` corresponds to the first argument, `$2` corresponds to the second argument, and so on. The number after the `$` indicates the position of the argument.

  * Example:

    Consider the following script named "myscript.sh"

    ```shell
    #!/bin/bash
    
    echo "Script name: $0"
    echo "First argument: $1"
    echo "Second argument: $2"
    ```

    If you execute the script with command-line arguments like this:

    ```plain
    $ ./myscript.sh arg1 arg2
    ```

    The script will output:

    ```plain
    Script name: ./myscript.sh
    First argument: arg1
    Second argument: arg2
    ```

    
