[Home](../../) | [Notes](../../notes) | [Projects](../) > My UNIX Shell

# My UNIX Shell 



## Introduction

A UNIX shell program which is capable of running both the built-in commands and the external commands.

The shell is the primary means for user interaction with a Unix system. The shell is a program like any other program and has the capabilities built into the logic to implement the functionality users require.



## Objective

* Understand how the UNIX shell manages and manipulates commandline user inputs internally.

* Understand how the built-in commands and external commands get executed.

* Understand the essence of `PATH` variable.

* Understand how a child process is spawned and runs its own tasks.




## Development Environment

* **Operating System**

  Ubuntu 22.04.1 LTS (Kernel version: 5.15.0-52-generic)

* **Compiler**

  GCC version 11.3.0 (Ubuntu 11.3.0-1ubuntu1~22.04)

* **Debugger**

  GDB version 12.0.90 (Ubuntu 12.0.90-0ubuntu1)



## Specification

### Built-in Commands

* `echo` - Display its argument to the console. It will assume a carriage return. If the option `-n` is entered the carriage
  return will not be issued.
* `PSI` - Change the display of the prompt. The initial prompt will be the `$`.
* `cat` - Display the contents of a file.
* `cp` - Copy the contents of a file.
* `rm` - Delete the file.
* `mkdir` - Create a directory.
* `rmdir` - Delete the directory.
* `exit` - Exit the shell program.

### External Commands

* User-defined command
* For other external commands, search `PATH`.

### Error Handling

* If an incorrect command is entered a “Usage” message will be displayed and the prompt will be redisplayed.



## Srouce Code

### myshell.c

```assembly
/******************************************************************************
 * File Name    : myshell.c
 * Author       : Kyungjae Lee
 * Description  : A UNIX shell program which is capable of running built-in 
                  commands and external commands.
 * Date Created : 02/20/2022
 * Last Updated : 03/13/2022
 *****************************************************************************/ 

#include <stdio.h>
#include <string.h>         /* strcmp, strtok */
#include <unistd.h>
#include <errno.h>          /* EEXIST, ENOENT */
#include <dirent.h>         /* DIR, opendir */
#include <linux/limits.h>   /* PATH_MAX */
#include <stdlib.h>         /* getenv */
#include <unistd.h>         /* fork, execv */
#include <sys/wait.h>       /* wait */
#include <sys/stat.h>       /* mkdir */

#define MAX_LEN     1024
#define MAX_ARGS    64
#define DELIM       " \t\n"

/* function prototypes */
void read_cmdline(char *p_line);
void execute_cmdline(char *p_line, int *p_argc, char *p_argv[]);
void parse_cmdline(char *p_line, int *p_argc, char *p_argv[]);
void my_echo(char *p_line, int *p_argc, char *p_argv[]);
void my_PS1(char *p_line);
void my_cat(char *p_line, int *p_argc, char *p_argv[]);
void my_cp(char *p_line, int *p_argc, char *p_argv[]);
void my_mkdir(char *p_line, int *p_argc, char *p_argv[]);
void my_rmdir(char *p_line, int *p_argc, char *p_argv[]);
void my_rm(char *p_line, int *p_argc, char *p_argv[]);
int is_file(char *name);
void ext_cmd(char *p_line, int *p_argc, char *p_argv[]);
void fork_exec_wait(char *p_buf, char *p_argv[]);
char* search_path(char *p_cmd, char *p_buf, int size);
char* search_cwd(char *p_cmd, char *p_buf, int size);
void quit(char *message, int exit_status);
void arg_check(int args, int argc, char *message, int exit_status);

/* 
 * Variable Naming Conventions
 *  - prefix 'g_': global variables
 *  - prefix 'l_': local variables
 *  - prefix 'p_': function parameters
 */

/* global variables */
extern int errno;
int g_exitflag = 0;
char g_prompt[MAX_LEN] = "$ ";


int main(int argc, char *argv[])
{
    /* local variables */
    int l_argc = 0;
    char *l_argv[MAX_ARGS] = {0};
    char l_cmdline[MAX_LEN] = {0};

    /* mysh.c main loop */
    while (1)
    {
        /* print the command prompt */
        printf("%s", g_prompt);

        /* read the command line */
        read_cmdline(l_cmdline);

        /* execute the command line */
        execute_cmdline(l_cmdline, &l_argc, l_argv);

        /* exit the program if user enters 'exit' command */
        if (g_exitflag)
            break;
    }

    return 0;
} /* end main */


/*
 * Function     : read_command
 * Parameters   : char *p_line
 * Return type  : void
 * Description  : Reads in and stores the command line into the passed 
 *                character array, returns none.
 */
void read_cmdline(char *p_line)
{
    /* local variables */
    int i = 0;

    /* read in the command line from stdin */
    fgets(p_line, MAX_LEN, stdin);

    /* skip till the terminating '\n' character */
    for (i = 0; p_line[i] != '\n'; ++i)
    {
        /* intentionally left blank */
    }

    /* replace the terminating '\n' character with '\0' character */
    p_line[i] = '\0';

    return;
} /* end read_cmdline */


/*
 * Function     : parse_cmdline
 * Parameters   : char *p_line, int *p_argc, char *p_argv[]
 * Return type  : void
 * Description  : Parses and tokenizes the command line.
 */
void parse_cmdline(char *p_line, int *p_argc, char *p_argv[])
{
    /* local variables */
    int i = 0;
    char *l_token;

    /* tokenize the command line */
    l_token = strtok(p_line, DELIM);

    /* repeat storing tokenized command line pieces into 'p_argv' */
    for (i = 0; l_token != NULL; ++i)
    {
        if (i < (MAX_ARGS - 1))
        {
            p_argv[i] = l_token;
            l_token = strtok(NULL, DELIM);
        }
    }

    /* update 'p_argc' */
    *p_argc = i;

    /* mark the end of 'p_argv' */
    p_argv[i] = 0;

    return;
} /* end parse_cmdline */


/*
 * Function     : execute_cmdline
 * Parameters   : char *p_line, int *p_argc, char *p_argv[]
 * Return type  : void
 * Description  : Executes command line. (Identifies the command from the
 *                read-in command line and calls the corresponding handler.
 *                Alerts the user if the command was not found.
 */
void execute_cmdline(char *p_line, int *p_argc, char *p_argv[])
{
    /* local variables */
    char l_line[MAX_LEN] = {0};
    char *l_token;

    /* make a copy of the read-in command line */
    strcpy(l_line, p_line);

    /* tokenize the command part only */
    l_token = strtok(l_line, DELIM);

    /* 'echo' command handler */
    if (!strcmp(l_token, "echo"))
    {
        my_echo(p_line, p_argc, p_argv);
    }
    /* 'PS1' command handler */
    else if (!strncmp(p_line, "PS1=", 4))
    {
        my_PS1(p_line);
    }
    /* 'cat' command handler */
    else if (!strcmp(l_token, "cat"))
    {
        my_cat(p_line, p_argc, p_argv);
    }
    /* 'cp' command handler */
    else if (!strcmp(l_token, "cp"))
    {
        my_cp(p_line, p_argc, p_argv);
    }
    /* 'rm' command handler */
    else if (!strcmp(l_token, "rm"))
    {
        my_rm(p_line, p_argc, p_argv);
    }
    /* 'mkdir' command handler */
    else if (!strcmp(l_token, "mkdir"))
    {
        my_mkdir(p_line, p_argc, p_argv);
    }
    /* 'rmdir' command handler */
    else if (!strcmp(l_token, "rmdir"))
    {
        my_rmdir(p_line, p_argc, p_argv);
    }
    /* 'exit' command handler */
    else if (!strcmp(l_token, "exit"))
    {
        g_exitflag = 1;
    }
    /* if command does not match any of the above built-in commands */
    else
    {
        /* call the external command handler */
        ext_cmd(p_line, p_argc, p_argv);
    }

    return;
} /* end execute_cmdline */


/*
 * Function     : ext_cmd
 * Parameters   : char *p_line, int *p_argc, char *p_argv[]
 * Return type  : void
 * Description  : Handles the potential external commands, informs the user if
 *                the entered command was not found.
 */
void ext_cmd(char *p_line, int *p_argc, char *p_argv[])
{
    char l_buffer[PATH_MAX];    /* to store the path of the command if found */
    int l_is_file = 0;

    /* parse the command line */
    parse_cmdline(p_line, p_argc, p_argv);

    /* if an abolute path was entered */
    if (*p_argv[0] == '/')
    {
        l_is_file = is_file(p_argv[0]);

        /* if the path represents a file */
        if (l_is_file == 1)
        {
            /* run the command */
            fork_exec_wait(p_argv[0], p_argv);
        }
        /* if the path represents a directory */
        else if (l_is_file == 0)
        {
            printf("%s: is a directory\n", p_argv[0]);
            return;
        }
        /* if the path was not found */
        else if (l_is_file == 2)
        {
            printf("%s: No such file or directory\n", p_argv[0]);
            return;
        }
    }
    /* search the PATH */
    else if (search_path(p_argv[0], l_buffer, PATH_MAX))
    {
        /* run the command */
        fork_exec_wait(l_buffer, p_argv);
    }
    /* search relative to the current working directory */
    else if (search_cwd(p_argv[0], l_buffer, PATH_MAX))
    {
        l_is_file = is_file(l_buffer);

        /* if the path represents a file */
        if (l_is_file == 1)
        {
            /* run the command */
            fork_exec_wait(l_buffer, p_argv);
        }
        /* if the path represents a directory */
        else if (l_is_file == 0)
        {
            printf("%s: is a directory\n", p_argv[0]);
            return;
        }
        /* if the path was not found */
        else if (l_is_file == 2)
        {
            printf("%s: No such file or directory\n", p_argv[0]);
            return;
        }
    }
    else
    {
        printf("Command '%s' not found.\n", p_argv[0]);
    }
} /* end exit_cmd */


/*
 * Function     : fork_exec_wait
 * Parameters   : char *p_buf, char *p_argv[]
 * Return type  : void
 * Description  : forks a child process to run the passed commnd. Child execs a
 *                new program, parent waits for the child process to die.
 */
void fork_exec_wait(char *p_buf, char *p_argv[])
{
    int l_status;
    int pid;

    /* fork error */
    if ((pid = fork()) < 0)
    {
        quit("fork", 1);
    }
    /* child */
    else if (pid == 0)
    {
        if ((execv(p_buf, &p_argv[0]) < 0))
        {
            exit(200);
        }
    }
    /* parent */
    else
    {
        wait(&l_status);
        /*
        fprintf(stderr, "Exit status: %d\n", WEXITSTATUS(l_status));
        */
    }
} /* end fork_exec_wait */


/*
 * Function     : search_path
 * Parameters   : char *p_cmd, char *p_buf, int size
 * Return type  : char*
 * Description  : Searches the command in the PATH, copy the path to the
 *                command to the buffer if found, returns the pointer to the 
 *                buffer, NULL otherwise.
 */
char* search_path(char *p_cmd, char *p_buf, int size)
{
    char l_path[PATH_MAX] = {0};
    char l_pathtok[PATH_MAX] = {0};
    char *l_p;

    if ((l_p = getenv("PATH")) == NULL)
    {
        /* environment variable 'PATH' not found */
        printf("Environment variable 'PATH' not found.\n");
        return NULL;
    }

    strcpy(l_path, l_p);

    /* PATH search algorithm */
    l_p = strtok(l_path, ":");

    while (l_p)
    {
        strcpy(l_pathtok, l_p); 
        strcat(l_pathtok, "/");
        strcat(l_pathtok, p_cmd);

        if (access(l_pathtok, X_OK) == 0)
        {
            strcpy(p_buf, l_pathtok);
            return p_buf;
        }

        l_p = strtok(NULL, ":");
    }

    /* command not found in the PATH variable */
    return NULL;
} /* end search_path */


/*
 * Function     : search_cwd
 * Parameters   : char *p_cmd, char *p_buf, int size
 * Return type  : char*
 * Description  : Searches the command relative to the current working directory,
 *                returns the pointer to the where the found command's absolute
 *                path stored, NULL otherwise.
 */
char* search_cwd(char *p_cmd, char *p_buf, int size)
{
    char *l_p;

    if ((getcwd(p_buf, PATH_MAX)) == NULL)
    {
        /* getcwd not successful */
        return NULL;
    }

    strcat(p_buf, "/");
    strcat(p_buf, p_cmd);

    if (access(p_buf, X_OK) == 0)
    {
        return p_buf; 
    }
    else
    {
        /* Command not found in the current working directory */
        return NULL;
    }
} /* end search_cwd */


/*
 * Function     : my_echo
 * Parameters   : char *p_line, int *p_argc, char *p_argv[]
 * Return type  : void
 * Description  : Displays its argument to the console.
 */
void my_echo(char *p_line, int *p_argc, char *p_argv[])
{
    /* local variables */
    int i = 0;
    char l_line[MAX_LEN] = {0};
    char *l_token;

    /* make a copy of read-in command line */
    strcpy(l_line, p_line);

    /* if argument is a string wrapped in a pair of double quotes */
    if (strstr(l_line, "\"") != NULL)
    {
        /* tokenize the command line to check if option '-n' is entered */
        l_token = strtok(l_line, " ");
        l_token = strtok(NULL, " ");

        /* if option '-n' is found */
        if (!strcmp(l_token, "-n"))
        {
            /* make a copy of read-in command line */
            strcpy(l_line, p_line);

            /* tokenize command line to retrive double quote wrapped string */
            l_token = strtok(l_line, "\"");
            l_token = strtok(NULL, "\"");

            /* print the token without '\n' at the end */
            printf("%s", l_token);
        }
        /* if option '-n' is not found */
        else
        {
            /* make a copy of read-in command line */
            strcpy(l_line, p_line);

            /* tokenize command line to retrive double quote wrapped string */
            l_token = strtok(l_line, "\"");
            l_token = strtok(NULL, "\"");

            /* print the token with '\n' at the end */
            printf("%s\n", l_token);
        }
    }
    /* if argument is a string not wrapped in a pair of double quotes */
    else
    {
        /* parse the command line */
        parse_cmdline(p_line, p_argc, p_argv);

        /* if argument is missing */
        if (p_argv[1] == NULL)
        {
            puts("");
            return;
        }
        /* when option '-n' is entered */
        else if (!strcmp(p_argv[1], "-n"))
        {
            /* print the tokens without '\n' at the end */
            for (i = 2; p_argv[i] != NULL; ++i)
            {
                printf("%s ", p_argv[i]);
            }
        }
        /* when no option was entered */
        else
        {
            /* print the tokens with 'new line' at the end */
            for (i = 1; p_argv[i] != NULL; ++i)
            {
                printf("%s ", p_argv[i]);
            }
            puts("");
        }
    }

    return;
} /* end my_echo */


/*
 * Function     : my_PS1
 * Parameters   : char *p_line
 * Return type  : void
 * Description  : Changes the display of the prompt.
 */
void my_PS1(char *p_line)
{
    /* local variables */
    char l_line[MAX_LEN] = {0};
    char *l_token;

    /* make a copy of read-in command line */
    strcpy(l_line, p_line);
    
    /* if argument is a string wrapped in a pair of double quotes */
    if (strstr(l_line, "\"") != NULL)
    {
        l_token = strtok(l_line, "\"");
        l_token = strtok(NULL, "\"");
    }
    /* if argument is a string not wrapped in a pair of double quotes */
    else
    {
        /* make a copy of read-in command line */
        strcpy(l_line, p_line);

        /* read in and discard 'PS1=' part */
        l_token = strtok(l_line, " =\n");
        
        /* if nothing follows 'PS1=' */
        if ((l_token = strtok(NULL, " =\n")) == NULL)
        {
            /* Ubuntu linux allows an 'empty string' to be a prompt */
            l_token = "";
        } /* else, 'l_token' will contain a non-empty string that follows
            'PS1=' that is not wrapped in a pair of double quotes */
    }

    /* set the new prompt to 'l_token' */
    strcpy(g_prompt, l_token);

    return;
} /* end my_PS1 */


/*
 * Function     : my_cat
 * Parameters   : char *p_line
 * Return type  : void
 * Description  : Displays the contents of a file.
 */
void my_cat(char *p_line, int *p_argc, char *p_argv[])
{
    /* local variables */
    int i = 0;
    FILE *l_fp;
    char l_ch;

    /* parse the command line */
    parse_cmdline(p_line, p_argc, p_argv);

    /* if argument is missing */
    if (*p_argc == 1)
    {
        /* original 'cat' command, when there's no argument, echoes whatever 
           the user enters to the screen and repeats it until 'Ctrl-D' is keyed
           in, but this program will simply output the error message and return
           since the requirement doesn't specify this functionality */
        printf("%s: missing file operand\n", p_argv[0]); 

        return;
    }

    /* display the contents of file */
    for (i = 1; p_argv[i] != NULL; ++i)
    {
        /* open the file */
        l_fp = fopen(p_argv[i], "r");

        /* if file does not exist */
        if (l_fp == NULL)
        {
            printf("%s: %s: No such file or directory\n", p_argv[0], p_argv[i]);

            continue;
        }

        /* print the file contens to the screen */
        l_ch = fgetc(l_fp);
        while (l_ch != EOF)
        {
            printf("%c", l_ch);
            l_ch = fgetc(l_fp);
        }

        /* close the file */
        fclose(l_fp);
    }


    return;
} /* end my_cat */


/*
 * Function     : my_cp
 * Parameters   : char *p_line, int *p_argc, char *p_argv[]
 * Return type  : void
 * Description  : Copies the contents of a file.
 */
void my_cp(char *p_line, int *p_argc, char *p_argv[])
{
    /* local variables */
    FILE *l_sfp;            /* source file pointer */
    FILE *l_dfp;            /* destination file pointer */
    char *l_sfilename;      /* source file name */
    char *l_dfilename;      /* destination file name */
    char l_ch;
    
    /* parse the command line */
    parse_cmdline(p_line, p_argc, p_argv);

    /* if arguments are missing */
    if (*p_argc == 1)
    {
        printf("%s: missing file operand\n", p_argv[0]);
        return;
    }
    /* if only one argument was entered */
    else if (*p_argc == 2)
    {
        printf("%s: missing destination file operand after '%s'\n", 
            p_argv[0], p_argv[1]);
        return;
    }

    /* set source & destination file names */
    l_sfilename = p_argv[1];
    l_dfilename = p_argv[2];

    /* open the source file */
    l_sfp = fopen(l_sfilename, "r");

    /* if source file does not exist */
    if (l_sfp == NULL)
    {
        printf("%s: cannot stat '%s': No such file or directory\n", 
            p_argv[0], p_argv[1]);
        return;
    }

    /* open the destination file */
    l_dfp = fopen(l_dfilename, "w");

    /* if the destination fail to open */
    if (l_dfp == NULL)
    {
        printf("%s: unable to open destination file '%s'\n", 
            p_argv[0], p_argv[2]);
        return;
    }

    /* copy the source file contents to the destination file */
    l_ch = fgetc(l_sfp);
    while (l_ch != EOF)
    {
        fputc(l_ch, l_dfp);
        l_ch = fgetc(l_sfp);
    }

    /* close the files */
    fclose(l_sfp);
    fclose(l_dfp);

    return;
} /* end my_cp */


/*
 * Function     : my_mkdir
 * Parameters   : char *p_line, int *p_argc, char *p_argv[]
 * Return type  : void
 * Description  : Creates a directory
 */
void my_mkdir(char *p_line, int *p_argc, char *p_argv[])
{
    /* local variables */
    int i = 0;
    char *l_path;
    int l_ret = 0;

    /* parse the command line */
    parse_cmdline(p_line, p_argc, p_argv);
    
    /* if arguments are missing */
    if (*p_argc == 1)
    {
        printf("%s: missing operand\n", p_argv[0]);
        return;
    }

    /* create directory */
    for (i = 1; p_argv[i] != NULL; ++i)
    {
        l_path = p_argv[i];

        l_ret = mkdir(l_path, 0775);    /* default permission: 0775 */

        /* if successful */
        if (!l_ret)
        {
            /* report nothing */
        }
        /* if not successful */
        else
        {
            /* if directory already exists */
            if (errno == EEXIST)
            {
                printf("%s: cannot create directory '%s': File exists\n",
                    p_argv[0], p_argv[i]);
            }
        }

        /* clear error flags */
        errno = 0;
    }

    return;
} /* end my_mkdir */


/*
 * Function     : my_rmdir
 * Parameters   : char *p_line, int *p_argc, char *p_argv[]
 * Return type  : void
 * Description  : Removes a directory.
 */
void my_rmdir(char *p_line, int *p_argc, char *p_argv[])
{
    /* local variables */
    int i = 0;
    char *l_path;
    int l_ret = 0;

    /* parse the command line */
    parse_cmdline(p_line, p_argc, p_argv);

    /* if argument is missing */
    if (*p_argc == 1)
    {
        printf("%s: missing operand\n", p_argv[0]);
        return;
    }
    
    /* remove directory */
    for (i = 1; p_argv[i] != NULL; ++i)
    {
        l_path = p_argv[i];

        l_ret = rmdir(l_path);

        /* if successful */
        if (!l_ret)
        {
            /* report nothing */
        }
        /* if not successful */
        else
        {
            /* if directory is not found */
            if (errno == ENOENT)
            {
                printf("%s: failed to remove '%s': No such file or directory\n", 
                    p_argv[0], p_argv[i]);
            }
            /* if it is not a directory */
            else if (errno == ENOTDIR)
            {
                printf("%s: failed to remove '%s': Not a directory\n", 
                    p_argv[0], p_argv[i]);
            }
            /* if directory is not empty */
            else if (errno == ENOTEMPTY)
            {
                printf("%s: failed to remove '%s': Directory not empty\n", 
                    p_argv[0], p_argv[i]);
            }
        }

        /* clear error flags */
        errno = 0;
    }

    return;
} /* end my_rmdir */


/*
 * Function     : my_rm
 * Parameters   : char *p_line, int *p_argc, char *p_argv[]
 * Return type  : void
 * Description  : Deletes a file.
 */
void my_rm(char *p_line, int *p_argc, char *p_argv[])
{
    /* local variables */
    int i = 0;
    char *l_path;
    int l_ret = 0;

    /* parse the command line */
    parse_cmdline(p_line, p_argc, p_argv);

    /* if argument is missing */
    if (*p_argc == 1)
    {
        printf("%s: missing operand\n", p_argv[0]);
        return;
    }
    
    /* loop through the arguments, remove files only */
    for (i = 1; p_argv[i] != NULL; ++i)
    {
        l_path = p_argv[i];

        /* if directory, do not remove and report the user */
        if (is_file(l_path) == 0)
        {
            printf("%s: cannot remove '%s': Is a directory\n", 
                p_argv[0], l_path);
        }
        /* if file not found */
        else if (is_file(l_path) == 2)
        {
            printf("%s: cannot remove '%s': No such file or directory\n", 
                p_argv[0], p_argv[i]);
        }
        /* if file, remove */
        else if (is_file(l_path) == 1)
        {
            l_ret = remove(l_path);

            /* if successful */
            if (!l_ret)
            {
                /* report nothing */
            }
            else
            {
                /* 'errno' handling goes here (left blank since not required by
                   the requirement) */
            }

            /* clear error flags */
            errno = 0;
        }
    }

    return;
} /* end my_rm */


/*
 * Function     : is_file
 * Parameters   : char *name
 * Return type  : int
 * Description  : Determines if the entity with the passed name is a 'directory
 *                file' or an 'ordinary file' or does not exist and returns a
 *                corresponding integer value.
 */
int is_file(char *name)
{
    /* local variables */
    DIR *l_dir = opendir(name);

    /* if directory */
    if (l_dir != NULL)
    {
        closedir(l_dir);
        return 0;
    }

    /* if not directory */
    if (errno == ENOTDIR)
    {
        return 1;
    }

    /* if not exist */
    if (errno == ENOENT)
    { 
        return 2;
    }

    return -1;
} /* end is_file */


/*
 * Function     : quit
 * Parameters   : char *message, int exit_status
 * Return type  : void
 * Description  : Prints a passed message and terminates the program.
*/
void quit(char *message, int exit_status)
{
    fprintf(stderr, message);
    exit(exit_status);
} /* end quit */
```

### myls.c

```c
/******************************************************************************
 * File Name    : myls.c
 * Author       : Kyungjae Lee
 * Description  : C program to read a directory and display the contents.
 * Date Created : 02/20/2022
 * Last Updated : 03/13/2022
 *****************************************************************************/ 

#include <dirent.h>         /* for DIR and struct dirent */
#include <stdio.h>         
#include <stdlib.h>         
#include <string.h>         

/* function prototypes */
void quit(char *message, int exit_status);


int main(int argc, char **argv)
{
    DIR *dir;                   /* returned by opendir */
    struct dirent *direntry;    /* returned by readdir */
    int i = 0;

    /* if argument does not exist */
    if (argc == 1)
    {
        argv[1] = ".";      /* read current directory by default */
        argv[2] = NULL;     /* specify termination point */

        /* check if directory open fails */
        if ((dir = opendir(argv[1])) == NULL)   
        {
            quit("opendir", 1);                
        }

        /* until entries are exhauseted */
        while ((direntry = readdir(dir)) != NULL)  
        {
            /* ignore hidden files */
            if (*(direntry->d_name) != '.')
            {
                printf("%s  ", direntry->d_name);
            }
        }

        closedir(dir);

        puts("");
    }
    /* if arguments exist */
    else if (!strcmp(argv[1], "-a"))
    {
        if (argc == 2)
        {
            argv[2] = ".";     /* read current directory by default */
            argv[3] = NULL;    /* specify termination point */
        }

        i = 2;
       
        while (argv[i])
        {
            /* check if directory open fails */
            if ((dir = opendir(argv[i])) == NULL)   
            {
                quit("opendir", 1);                
            }

            /* until entries are exhauseted */
            while ((direntry = readdir(dir)) != NULL)  
            {
                printf("%s  ", direntry->d_name);
            }

            closedir(dir);
            i++;

            puts("");
        }
    }
    /* when used without option '-a' */
    else
    {
        i = 1;
       
        while (argv[i])
        {
            /* check if directory open fails */
            if ((dir = opendir(argv[i])) == NULL)   
            {
                quit("opendir", 1);                
            }

            /* until entries are exhauseted */
            while ((direntry = readdir(dir)) != NULL)  
            {
                /* ignore hidden files */
                if (*(direntry->d_name) != '.')
                {
                    printf("%s  ", direntry->d_name);
                }
            }

            closedir(dir);
            i++;

            puts("");
        }
    }

    return 0;
} /* end main */


/*
 * Function     : quit
 * Parameters   : char *message, int exit_status
 * Return type  : void
 * Description  : Prints a passed message and terminates the program.
*/
void quit(char *message, int exit_status)
{
    fprintf(stderr, message);
    exit(exit_status);
} /* end quit */
```
