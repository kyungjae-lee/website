[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > GDB Cheat Sheet

# GDB Cheat Sheet



## GNU Debugger (GDB) Cheat Sheet

```shell
$ g++ -g source.cpp -o program
$ gdb program.exe
```

> `-g` option embeds symbolic debug information into the output file `program`. This debug info includes:
>
> * Function names
> * Variable names and types
> * Line numbers in source code
> * Mapping between binary instructions and source code

| Command                                    | Description                                                  |
| ------------------------------------------ | ------------------------------------------------------------ |
| `break <location>` or <br />`b <location>` | Set a breakpoint (e.g., `b main`, `b 11`, `b file.cpp:25`, `b func`). |
| `run` or `r`                               | Start the program from the beginning.                        |
| `delete`                                   | Delete all breakpoints. Use `delete <n>` to remove a specific one. |
| `disable <n>` / `enable <n>`               | Disable or enable a breakpoint.                              |
| `info breakpoints` or `i b`                | List current breakpoints and their status.                   |
| `next` or `n`                              | Execute the next line (step over function calls).            |
| `step` or `s`                              | Step into the function call on the current line.             |
| `finish`                                   | Run until the current function returns.                      |
| `continue` or `c`                          | Resume execution after a breakpoint.                         |
| `print <expr>` or `p <expr>`               | Print the value of an expression or variable.                |
| `display <expr>`                           | Auto-print the value of an expression each time the program stops. |
| `undisplay <n>`                            | Remove a display expression.                                 |
| `backtrace` or `bt`                        | Show call stack (current function and callers).              |
| `frame <n>`                                | Switch to frame number `n` in the call stack.                |
| `up` / `down`                              | Move one frame up or down (in terms of stack number where `#0` being the top frame) the stack. |
| `info locals`                              | Show local variables in the current frame.                   |
| `info args`                                | Show function arguments in the current frame.                |
| `list` or `l`                              | Show source code near the current line or a specific location. |
| `set var <var>=<value>`                    | Change the value of a variable.                              |
| `watch <expr>`                             | Set a watchpoint (pause when the expression changes).        |
| `info watchpoints`                         | List watchpoints.                                            |
| `stepi` or `si`                            | Step one machine instruction.                                |
| `nexti` or `ni`                            | Step over one machine instruction.                           |
| `quit` or `q`                              | Exit GDB.                                                    |



## Sample Program to Practice GDB

### Source (`gdb.cpp`)

```cpp
#include <iostream>
using namespace std;

void f3()
{
    int f3_var = 30;
    cout << f3_var << end;
}

void f2()
{
    int f2_var = 20;
    cout << f2_var << end;
    f3();
}

void f1()
{
    int f1_var = 10;
    cout << f1_var << end;
    f2();
}

int main(int argc, char *argv[])
{
	int main_var = 50;
    cout << main_var << endl;
    f1();
    return 0;
}
```

Compile the program:

```plain
g++ -g gdb.cpp
```

Attach GDB:

```plain
gdb .\a.exe
```

Set breakpoints:

```plain
(gdb) break main
Breakpoint 1 at 0x14000152c: file gdb.cpp, line 26.
(gdb) b f3
Breakpoint 2 at 0x140001468: file gdb.cpp, line 6.
```

View breakpoints:

```plain
(gdb) info breakpoints
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x000000014000152c in main(int, char**) at gdb.cpp:26
2       breakpoint     keep y   0x0000000140001468 in f3() at gdb.cpp:6
```

Run the program:

```plain
(gdb) run
Starting program: D:\workspace\cpp\a.exe 
[New Thread 23204.0x21f4]
[New Thread 23204.0x1ef0]
[New Thread 23204.0x34e4]

Thread 1 hit Breakpoint 1, main (argc=1, argv=0xf2080) at gdb.cpp:26
26              int main_var = 50;
```

Continue to the next breakpoint:

```plain
(gdb) continue
Continuing.
50
10
20

Thread 1 hit Breakpoint 2, f3 () at gdb.cpp:6
6           int f3_var = 30;
```

View call stack:

```plain
(gdb) backtrace
#0  f3 () at gdb.cpp:6
#1  0x00007ff677a314d2 in f2 () at gdb.cpp:14
#2  0x00007ff677a31511 in f1 () at gdb.cpp:21
#3  0x00007ff677a3155c in main (argc=1, argv=0xf2080) at gdb.cpp:28
```

> `#n` refers to each stack frame number.

View local variables:

```plain
(gdb) info locals
f3_var = 0
```

View the value of a local variable in a different stack frame:

```plain
(gdb) frame 2
#2  0x00007ff677a31511 in f1 () at gdb.cpp:21
21          f2();
(gdb) print f1_var
$1 = 10
```

> First, you need to go to the stack frame in which the variable you want to print is declared.

View the source code near the current line:

```plain
(gdb) list
16
17      void f1()
18      {
19          int f1_var = 10;
20          cout << f1_var << endl;
21          f2();
22      }
23
24      int main(int argc, char *argv[])
25      {
```

View the current line:

```plain
(gdb) frame
#2  0x00007ff677a31511 in f1 () at gdb.cpp:21
21          f2();
```

Return to the top stack frame:

```plain
(gdb) f 0 
#0  f3 () at gdb.cpp:6
6           int f3_var = 30;
```

Run until the current function returns:
```plain
(gdb) finish
Run till exit from #0  f3 () at gdb.cpp:6
30[New Thread 23204.0x2738]

f2 () at gdb.cpp:15
15      }
```
