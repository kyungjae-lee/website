[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > CLI Cheat Sheet

# CLI Cheat Sheet



## Quick Copy & Paste Trick

* If you are using a mouse, 
  1. Highlight some text by holding down the left mouse button and dragging the mouse over it, or double-clicking a word. (It is copied into a buffer maintained by your system.)
  2. Press the middle mouse button to paste the text at the cursor location.



## Command Line Editing

### Cursor Movement Commands

* `Ctrl` + `A` - Move cursor to the beginning of the line.
* `Ctrl` + `E` - Move cursor to the end of the line.
* `Ctrl` + `F` - Move cursor forward one character; same as the right arrow key.
* `Ctrl` + `B` - Move cursor backword one character; same as the left arrow key.
* `Alt` + `F` - Move cursor forward one word.
* `Alt` + `B` - Move cursor backword one word.
* `Ctrl` + `F` - Clear the screen and move the cursor to the top-left corner. The clear command does the same thing.
* `Ctrl` + `xx` - Toggle between the start of line and current cursor position.

### Text Editing Commands

* `Ctrl` + `D` - Delete the character at the cursor location.
* `Ctrl` + `H` - Delete character before the cursor (backspace)
* `Ctrl` + `W` - Cut the word before the cursor to the clipboard.
* `Ctrl` + `K` - Cut the line after the cursor to the clipboard.
* `Ctrl` + `T` - Transpose (exchange) the character at the cursor location with the one preceding it.
* `Ctrl` + `Y` - Paste the last thing to be cut (yank)
* `Ctrl` + `_` - Undo
* `Alt` + `T` - Transpose the word at the cursor location with the one preceding it.
* `Esc` + `T` - Swap the last two words before the cursor.
* `Alt` + `L` - Convert the characters from the cursor location to the end of the word to lowercase.
* `Alt` + `U` - Convert the characters from the cursor location to the end of the word to uppercase.
* `Alt` + `Del` - Delete the word before the cursor.
* `Alt` + `D` - Delete the word afer the cursor

### Cut-and-Paste Commands

* `Ctrl` + `K` - Kill text from the cursor location to the end of line.
* `Ctrl` + `U` - Kill text from the cursor location to the beginning of the line.
* `Alt` + `D` - Kill text from the cursor location to the end of the current word.
* `Alt` + `Backspace` - Kill text from the cursor location to the beginning of the current word. If the cursor is at the beginning of a word, kill the previous word.
* `Ctrl` + `Y` - Yank text from the kill-ring and insert it at the cursor location.



## Linux Command Line Interface (CLI) Cheat Sheet

```plain
===============================================================================
                      LINUX COMMAND LINE INTERFACE (CLI)
===============================================================================

SHELL
=====
Shell is a program that takes command from the keyboard and give them to the
operating system to perform.


TERMINAL
========
Terminal is a tool which you can use to pass your shell commands. This is a
program that opens a window and lets you interact with the shell.

* Ctrl+Alt+t - open terminal
* Ctrl+Shift+t - open second terminal in a new tab


FILESYSTEM
==========
The files on linux system are also arranged in what we called a hierarchical
directory structure which means that they are organized in a tree-like pattern
of directories. (Under 'root' directory all files and folders are residing.)

* .     : current directory
* ..    : parent directory (one directory up)



===============================================================================
                                COMMANDS
===============================================================================

* pwd (present working directory)

    des. whenever you will open the terminal, by default the 'pwd' is your home
    directory ('home/username')

* cd (change working directory)

    syn. cd [dir]

    e.g. 
        cd /        : change the working directory to the root directory 
        cd ~        : change the working direcotry to your home directory
        cd          : same as 'cd ~'
        cd ..       : change the working directory to the parent directory
        cd Music    : change the working directory to the music directory
        cd My\ Doc  : change the working directory to the 'My Doc' directory
                     (to deal with directory whose name includes space, escape
                     sequence '\' must be used)
        cd "My Doc" : same as 'cd My\ Doc'
        cd 'My Doc' : same as 'cd My\ Doc'

    [!] Note: Both absolute/relative paths will work with 'cd' command

* ls (list)

    des. list content in the directory

    syn. ls [options] [f/dir]

    e.g. 
        ls Music/   : list content in the Music directory
        ls -l       : list in 'l'ong format
        ls -a       : list 'a'll files (including hidden files)
        ls -al      : list 'a'll files in 'l'ong format
        ls -S       : sort list by 'S'ize
        ls *.html   : list html files only
        ls *.*      : list files regardless of the file names and extensions
        ls > f1     : redirect the output of ls to the file 'f1' 
        ls -d */    : list 'd'irectories only
        ls -R       : list subdirectories 'R'ecursively

* clear

    des. clear the terminal (you can still see the history by scrolling up)

* cat
    
    des. display text files, combine copies of text files, create text files

    syn. cat [options] [f1] [f2] ...

    e.g. 
        cat         : display the input text ('Ctrl+D' to exit)
        cat f1      : display content of f1   
        cat f1 f2   : display content of f1 and f2 combined
        cat -b f1   : display content of f1 with line numbers to the
                      non-'b'lank lines
        cat -n f1   : display content of f1 with line 'n'umbers to all lines
        cat -s f1   : display content of f1 with consecutive blank lines
                      's'queezed to one blank line (will not change the content
                      of the file but just the display it this way for
                      convenience)
        cat -E f1   : display conetnes of f1 with 'E'nd of Line character '$'
                      appended to the end of each line)
        Ctrl+d      : indicate that this is the end of the file and get out of
                      the 'cat' command

* > (redirction)

    des. capture output from a file, command or program and send it as an input
         to another file, command or program

    syn. [output] > [f]

    e.g.
        cat > f     : redirect input text to the file f (overwrite the
                      existing conten)
        cat >> f    : redirect input text to the file f (append to the
                      existing content)
        cat f1 f2 > f3
                    : redirect combined content of the files f1, f2 to file f3
        cat f1 >> f2
                    : redirect  and append the content of the file f1 to f2
                      ('cat f1 f2 > f2' will not work)

* mkdir (make directory)

    syn. mkdiri [options] [dir]

    e.g.
        mkdir d     : make directory named 'd'
        mkdir -p dp/dc
                    : make directory structure (create 'p'arent directory dp
                      and then create child directory dc inside dp)
        mkdir --parents dp/dc
                    : same as 'mrdir -p dp/dc'
        mkdir -p dp/{dc1,dc2,dc3}
                    : make directory structure (create 'p'arent directory dp
                      and then create 3 child directories inside dp at once)

    [!] Note: 'mkdir -p dp/{dc1, dc2, dc3}' will cause an error due to spaces
              between dc's

* rmdir (remove directories)

    syn. mkdiri [options] [dir]
    
    e.g.
        rmdir a/b/c : remove the directory 'c' only
        rmdir -p a/b/c
                    : remove the whole directory structure a/b/c ('p'arents)
        rmdir -pv a/b/c
                    : remove the whole dirctory structure and show extended
                      information ('v'erbose)

    [!] Note: if a directory contains files in it, rmdir won't work for that
              directory

* rm (remove files or directories)

    syn. rm [options] [f/dir]

    e.g.
        rm -rv a/b/c/d  (where directory 'd' contains a file)
                    : remove the file in directory 'd' and then remove the
                      directory 'd' ('r'ecursive)
        rm -rv a    : remove recursively the directories a, b, c, d and any
                      files belong to the directory structure (just give the
                      parent directory, and 'rm' command will remove everything
                      under that directory)

    [!] Note: with the option 'r', 'rm' command removes only the given
              directory and its subdirectories (including the files) only and
              it does not remove the whole directory structure given.
                    
* cp (copy files and directories)

    syn. cp [options] [source f/dir] [destination f/dir]

    e.g.
        cp f1 f2    : if f2 didn't exist, create f2 and copy the content of f1
                      to f2
        cp f1 dir   : copy f1 to dir
        cp f1 f2 f3 dir
                    : copy f1, f2, f3 to dir (copy multiple files)
        cp -i f1 dir
                    : prompt before overwrite ('i'nteractive)
        cp -R dir1 dir2
                    : copy dir1 to dir2 (when dir1 contains files)
                      ('R'ecursive)
                    : if dir2 didn't exist, dir2 will be created and then the
                    contents of dir1 will be copied to dir2
                    : if dir2 did exist, dir1 will be copied into dir2 as a
                      sub-directory

* mv (move (rename) files)

    syn. mv [options] [source f/dir] [destination f/dir]

    e.g.
        mv f1 f2    : rename f1 to f2
        mv f1 dir   : move f1 to dir (overwrite the file named f1 that already
                      exists in dir)
        mv -i f1 dir  
                    : prompt before overwrite ('i'nteractive)
        mv dir1 dir2
                    : if dir2 did exist, dir1 will be copied into dir2 as a
                      sub-directory
                    : if dir2 didn't exist, rename dir1 to dir2

* less (read or search in files)

    syn. 

    e.g. 
        less f      : show the content of f from the begning
                    : 'up/down arrow' keys to navigte line by line
                    : 'space' key to navigate page by page (forward)
                    : 'B' to navigate page by page (backward)
                    : 'G' browse to the end of f
                    : '1g' or 'g' to go to the begining of f
                    : '/pattern' to forward highlight search 'pattern'
                        - n : next hit
                        - N : prev hit
                    : '?pattern' to backword highlight search 'pattern'
                        - n : next hit
                        - N : prev hit

* touch (change file timestampso)
    
    des. easiest way to create new empty files. also used to change timestamps
         on existing files or directories

    syn. touch [options] [f/dir]

    e.g.
        touch f     : create a new empty file f
                    : if f is an already exsisting file, 'touch' command
                      updates the timestamp of f

* nano (Nano's ANOther editor, inspired by Pico)

    des. nano is a small and friendly text editor and beside text editing Nano
         offers many extra features like an interactive search or replace, go
         to line or colum number, give indentation in the file or some other
         features.

    syn. nano [options] [f]

    e.g.
        nano f      : open f in nano
                    : create f if it didn't exist (f is actually created when
                      it is saved from nano - 'Ctrl+o Enter')

    [!] Note: get used to nano commands to be fluent (^G means Ctrl+g)

* sudo (superuser do)

    des. sudo allows user to some extra privileges as an administrator or a
         superuser (when user opens a terminal, by default, he/she does not
         have the administrative or root previleges)

    syn. sudo ...

    e.g.
        sudo /etc mkdir newdir
                    : if 'sudo' command is omitted, permission denied error
                      will occur
        sudo -s     : switch to the root user (switch to super user mode)

* top (display linux processes)

    des. top program provides a dynamic real-time view of a running system

    syn. top [options]

    e.g.
        top         : display system summary information as well as a list of
                      processes or threads currently being managed by linux
                      kernel (by default, refreshes every 3 seconds)
                    : press 's' to change refreshing frequency
                    : press 'i' to filter out idle processes (press 'i' once
                      again to go back to the complete view
                    : press 'k' to kill a process by its PID

* kill
    
    des. send a signal to a process

    syn. kill [options] [pid] [...]

    e.g.
        kill 3286   : kill the program with PID 3286
                    : if the program is not terminated with simple kill command
                      following 3 options can be used force terminating
                      - -9
                      - -SIGKILL
                      - -KILL

    [!] Note: when you do not know the PID of a certain process, you can get it
              by using 'pidof' command. if you do not even know the program
              name you want to kill, use either 'top' command or 'ps' command
              to find the PID.

* pidof

    des. find the process ID of a running program

    syn. pidof [options] [program]

    e.g.
        pidof unity-control-center
                    : find the PID of 'unity-control-center' program

* ps

    des. report a snapshot of the current processes

    syn. ps [options]

    e.g.
        ps -ux      : show a list of running processes used by current user
        ps -aux     : show a list of running processes used by all users
        ps -U username
                    : show a list of running processes used by a specific user
                      'username'
        ps -C procname
                    : show a list of running processes related to the program
                      'procname'

* echo

    des. display a line of text

    syn. echo [options] [string]

    e.g.
        echo hello  : display 'hello'
        echo "hello": wrapping the string with double quotes is a good
                      convention
        myvar="kj"  : assign "kj" to the variable 'myvar' (make sure there's no
                      space around '=')
        echo $myvar : display the content(value) of 'myvar'
        x=10        : assign 10 to variable 'x'
        echo "the value of x is $x"
                    : (output) the value of x is 10
        echo -e 'some \text'
                    : (output) some  ext
                    : 'e'nable interpretation of '\' as an escape sequence

    [!] Note: variables are valid only in the current session of terminal
              (when terminal is closed and then reopened, myvar is gone)
    [!] Note: echo command becomes more powerful when you are working with
              scripting

* permissions

    e.g.

    -rw-rw-r--      1     klee    klee    36    Sep 9   00:32   README.md
    ----------     ---    ----    ----    --    -------------   ---------
    symbolic      # of    owner   group   size    date/time     file name
    permissions  symbolic
                  links
         

    syn. symbolic permission classes (symbolic mode)    

          -         r   w   x         r   w   -         r   -   -
         ---        ---------         ---------         ---------
      [file type]  [owner/user]       [ group ]         [ other ]
          |             |
          |             +--> r : read
          |                  w : write
          |                  x : execute
          |
          |
          +---> - : file
                d : derectory
                c : character special file
                b : binary special file

    syn. octal permission (numerical representation of the file permission)

          -         r   w   x         r   w   -         r   -   -
         ---        ---------         ---------         ---------
      [file type]  [owner/user]       [ group ]         [ other ]

                    1   1   1         1   1   0         1   0   0
                   2^2 2^1 2^0       2^2 2^1 2^0       2^2 2^1 2^0
                    4   2   1         4   2   0         4   0   0
                    4 + 2 + 1         4 + 2 + 0         4 + 0 + 0
                    ---------         ---------         ---------
                        7                 6                 4

        +------------------------------------------------------------+
        |  r,w,x permissions |      binary       |       octal       |
        |============================================================|
        |        ---         |       000         |         0         |
        |------------------------------------------------------------|
        |        --x         |       001         |         1         |
        |------------------------------------------------------------|
        |        -w-         |       010         |         2         |
        |------------------------------------------------------------|
        |        -wx         |       011         |         3         |
        |------------------------------------------------------------|
        |        r--         |       100         |         4         |
        |------------------------------------------------------------|
        |        r-x         |       101         |         5         |
        |------------------------------------------------------------|
        |        rw-         |       110         |         6         |
        |------------------------------------------------------------|
        |        rwx         |       111         |         7         |
        +------------------------------------------------------------+

* chomod (change file mode bits)

    des. utility to change permission of files

    syn. chmod [options] [mode] [f/dir]

    e.g. symbolic permissions
        chmod o+x f : add(+) e'x'ecutable permission to 'o'ther for f
        chmod o+w f : add(+) 'w'rite permission to 'o'ther for f
        chmod g+w f : add(+) 'w'rite permission to 'g'roup for f
        chmod g-wx f: remove(-) read and write permissions from 'g'roup for f
        chmod ug=rwx f
                    : change 'u'ser and 'g'roup mode of f to 'rwx'
        chmod ugo-rwx f
                    : remove(-) remove all the permissions from all the users
        chmod a-rwx f
                    : same as 'chmod ugo-rwx f ('a'll)
        chmod u+rw,g=rw,o+r f
                    : work with multiple user's permissions
        chmod u-w dir (u=rx)
                    : user does not have write permission to dir
                    : user can read the content of dir
                    : user can 'cd' to dir
                    : user CANNOT create new files inside dir
        chmod u-r dir (u=wx)
                    : user does not have read permission to dir
                    : user can 'cd' to dir
                    : user CANNOT read the content of dir
        chmod u-x dir (u=rw)
                    : user does not have  execute permission to dir (user
                      CANNOT execute anything with dir)
                    : user can only read the names of content inside dir (even
                    the permissions of the files are '?' marked)
                    : user CANNOT 'cd' to dir
                    : user CANNOT access to any files in dir
        
    e.g. octal(numerical) permissions
        chmod 000 f : nobody has any permission for f
        chmod 755 f : give user 'rwx' permissions
                    : give group 'r-x' permissions
                    : give other 'r-x' permissions

* which

    des. return a path name of a file or command (locate a file)

    syn. which [options] [f]

    e.g. 
        which bash  : locate the command 'bash' (/usr/bin/bash)
        which f     : locate f
        which filefox
                    : /usr/bin/firefox
    
    [!] Noate: the only options available for 'which' is '-a'

* whatis
    
    des. display one-line manual page descriptions

    e.g.
        whatis ls   : show one-line manual page description of 'ls'

* useradd

    des. create a new user of update default new user information

    syn. useradd [options] LOGIN
         useradd -D
         useradd -D [options]

    e.g.
        sudo useradd username -m -s /bin/bash -g users -c "my comment"
                    : create the user 'username' with features specified by the
                      options
                      -m : create default home directory for this user
                      -s : allow the user to use the designated shell
                      -g : assign default group to this user
                      -c : provide comment to this user
                      ...
        sudo passwd username
                    : reset password for 'username'
        sudo passwd : reset password for root user

    [!] Note: new user can be found in '/home/'

* userdel

    des. delete a user account and related files

    syn. userdel [options] LOGIN

    e.g.
        sudo userdel username
                    : delete the user 'username' 
                    : but it does not delete the home directory for 'username'
                      (this is useful when a company wants to delete a resigned
                      person's account from the HR database but still wants to
                      keep the work the person did)
        sudo rm -r /home/username
                    : delete the leftover home directory of 'username'
        sudo userdel -r username
                    : delete the user 'username' as well as his/her home
                      directory

* groups

    des. print the groups a user is in

    syn. groups

    e.g.
        groups
        cat /etc/group
                    : show all the groups available on your system (groups are
                      added based on date-wise order, the newest group goes to
                      the bottom of the list)

                      e.g. sambashare:x:132:klee
                           ----------       ----
                             group          user
                         
* groupadd

    des. create a new group

    syn. groupadd [options] [group]

    e.g.
        sudo groupadd groupname
                    : add the group 'groupname' to the system ('/etc/group')

* groupdel
    
    des. delete a group

    syn. groupdel [options] [group]

    e.g.
        sudo groupdel groupname
                    : delete the group 'groupname' from the system
                      ('etc/group')

* gpasswd

    des. administer '/etc/group' and '/etc/gshadow' (every group can have
         administrators, members and a password)

    syn. gpasswd [options] [group]

    e.g. 
        sudo gpasswd -a username groupname
                    : add a user 'username' to the group 'groupname'
        sudo gpasswd -d username groupname
                    : 'd'elete a user 'username' from the group 'groupname'

* df

    des. report file system disk space usage

    syn. df [options] [f/dir]

    e.g.
        df          : show disk space usage
        df -h       : show disk space suage in 'h'uman readable format

* du

    des. estimate file space usage (estimate and display the disk space used by
         files)

    syn. du [options] [f/dir]

    e.g. 
        du         : estimate file space usage of the current directory 
        du /etc/   : estimate file space usage of the '/etc/' directory
        du -h      : ... in 'h'uman readable format 
        du -sh     : ... in 's'ummary and in 'h'uman readable format 
                   : if this command gives you 'cannot access' warnings, use
                     'sudo' command

* free

    des. display amount of free and used memory in the system as well as the
         buffer used by kernel

    syn. free [options]

    e.g.
        free -h     : display in 'h'uman readable format
        free -b     : display in 'b'yte
        free -k     : display in 'k'illobyte
        free -m     : display in 'm'egabyte
        free -g     : display in 'g'igabyte
        free -t     : display in 't'errabyte

* watch

    des. execute a program periodically, showing output fullscreen

    syn. watch [options] [command]

    e.g.
        watch free -m
                    : execute 'free -m' command periodically
        watch -n 0.5 free -m
                    : ... adjust frequency to 0.5 sec (default frequency is
                      2 sec) 

    [!] Note: 'Ctrl+c' to come out of the command

* head

    des. output the first part of files (10 lines by default)

    syn. head [options] [f]

    e.g. 
        head -n3 f  : display the first 3 lines of f
        head -3 f   : display the first 3 lines of f
        head f1 f2  : display the first parts of f1 and f2

* tail

    des. output the last part of files (10 lines by default)

    syn. tail [options] [f]

    e.g.
        tail -n3 f  : display the last 3 lines of f
        tail -f f   : stay in 'tail' command and output appended data as the
                      file grows ('f'ollow) 

* find

    des. search for files in a directory hirerarchy

    syn. find [dir] [options] [f]

    e.g.
        find /home/kjlee -name file.txt
                    : search 'file.txt' in the path '/home/kjlee' and show the
                      file location if it is found
        find / -mtime -3
                    : search all files created any time between 3 days ago and
                      now (this is useful when you don't know the name of the
                      file you want to search)
        find / -mtime 1
                    : search all files created exactly 1 day ago

    [!] Note: in case the 'Permission denied' warning appears, use 'sudo'
              command

* wc

    des. print newline, word, and byte counts for each file

    syn. wc [options] [f]

    e.g.
        wc file.txt :    1       6        42       file.txt
                        ---     ---      ----      --------
                        # of    # of     # of      name of
                        lines   words    chars     file
        wc -c f     : display just the byte counts (essentially the number of
                      'c'haracters) in f
        wc -l f     : display just the number of lines
        wc -w f     : display just the number of words
        wc -L f     : display just the length(# of chars) of the longest line

* cal

    des. display a calendar and the date of Easter

    syn. cal [options]

    e.g.
        cal         : display a default calendar (week days on the top)
        ncal        : display a calendar with week days on the left
        cal 2020    : display all the calendar months in year 2020
        cal 5 2020  : display the calendar for May, 2020 only
        cal -3      : display prev, current, next months

    [!] Note: '-1' is the default option for 'cal' command

* date

    des. print or set the system date and time

    syn. date [options] [format]
         date [format specifiers]

    e.g.
        date        : display current date and time
        date -s "11/20/2020 12:48:00"
                    : set the system date and time to '11/20/2020 12:48:00'
        date +%d%h%y
                    : 04Jan17
        date "+%d-%h-%y"
                    : 04-Jan-17
        date "+%d/%h/%y"
                    : 04/Jan/17
        date "+DATE: %m/%d/%y%nTIME: %H:%M:%S"
                    : DATE: 01/04/17
                      RIME: 22:25:13

    [!] Note: format specifiers of 'date' command
        +%m : specifies the month number
        +%h : specifies the abbreviated month(Jan-Dec)
        +%B : specifies the full month name
        +%d : specifies the day of the month
        +%y : specifies the year
        +%H, +%M and +%S : specifies the hour, minute, seconds
        +%D : specifies the date as mm/dd/yy
        +%T : specifies the time as hh:mm:ss

* apt-get

    des. APT package handling (install, remove, etc.) utility

    syn. apt-get ...

    e.g.
        sudo apt-get update
                    : resynchronize your local package files with server files
                    : these package files are stored in '/etc/apt/'
                      ('/etc/apt/sources.list' file contains paths to the
                      repositories of the packages)
        sudo apt-get install vim
                    : install 'vim' software package
                    : this command will display the list of  extra packages to
                      be installed (due to dependency) and how much space in
                      memory this installation will take up.
        sudo apt-get remove vim
                    : remove 'vim' software package
                    [!] Note: configuration files will NOT be removed! 
        sudo apt-get remove --purge vim
                    : remove 'vim' software package + configuration files
        sudo apt-get autoremove
                    : remove packages that were automatically installed to
                      satisfy the dependency for other packages and that are
                      now no longer needed
                    [!] Note: please check 'The following packages will be
                              REMOVED:' section in case you accidentally remove
                              necessary packages!

    [!] Note: 'yum' or 'dnf' command for redhat based OS(CentOS, Fedora, etc.)
              
* ifconfig (interface configuration)

    des. display or change the configuration of network interface on user's
         system

    syn. ifconfig [interface] [options]

    e.g. 
        ifconfig    : display the configuration of network interface on your
                      system
                    : 'eth0' suggests that you have WIRED Connection (ethernet
                      cable)to the internet (additional ethernet interfaces
                      will be named 'eth1', 'eth2', and so on. if this is the
                      case 'eth0' will be your main ethernet interface)
                    : 'wlan0' suggests that you have WIRELESS Connection to the
                      internet
                    : 'lo' suggests that you have LOOPBACK Interface
        ifconfig eth0
                    : display 'eth0' information only
        sudo ifconfig eth0 up
                    : enable internet connection through 'eth0'
        sudo ifconfig eth0 down
                    : disable internet connection through 'eth0'

* tar (tape archive; an archiving format just like 'zip' format)
    
    des. an archiving utility (create, maintain, modify and extract files which
         are archived in the tar format

    syn. tar [options] [compressed_name.tar] [f/dir]

    e.g.
        tar -cvf comp.tar dir
                    : compress 'dir' to 'comp.tar' file
                      - c   'c'reate an archive (create a tar file)
                      - v   'v'erbose (display the progress in the terminal
                            while creating the archive file 
                      - f   specify the 'f'ile name ('comp.tar' in this ex)
        tar -xvf comp.tar
                    : e'x'tract 'comp.tar' file
        tar -czvf comp.tar.gz dir
                    : compress 'dir' to 'comp.tar.gz', a gz(gzip) formatted
                      file 
                    [!] Note: 'z' flag must come after 'c' flag
        tar -xzvf comp.tar.gz dir
                    : e'x'tract 'comp.tar.gz' file

* grep (global regular expression print)

    des. print line that match specified patterns (case sensitive by default)

    syn. grep [options] [pattern] [f/dir]

    e.g.
        grep "pattern" file.txt
                    : print lines in 'file.txt' that contain 'pattern' 
                      (case sensitive by default)
        grep -i "pattern" file.txt
                    : print lines in 'file.txt' that contain 'pattern'
                      (case 'i'nsensitive)
        grep -n "pattern" file.txt
                    : print lines with line 'n'umbers in 'file.txt' that
                      contain 'pattern'
        grep "pattern" f1 f2 f3 f4
                    : print lines in multiple files (f1, f2, f3, f4) that
                      contain 'pattern' (file names are printed as well)
        grep -v "pattern" file.txt
                    : print lines in 'file.txt' that does NOT contain 'pattern'
                      (in'v'erse match)
        grep --help : display additional information on 'grep' command
                    
* netstat (network statistics)

    des. a command line tool used to display network connection, routing tables
         and a number of network interfaces. also can be used to view network
         protocol state statistics

    syn. netstat [options]

    e.g.
        netstat -a  : display 'a'll the connections available on your system
        netstat -a | less
                    : display the information from the first page on
        netstat -at | less
                    : display TCP connections only
        netstat -au | less
                    : display UDP connections only
        netstat -l | less
                    : display ports that are in 'l'istening state
        netstat -lt | less
                    : display 'l'istening TCP connections only
        netstat -lu | less
                    : display 'l'istening UDP connections only
        netstat -s | less
                    : display 's'tatistics
        netstat -pt | less
                    : display 'P'IDs of all TCP connections
        netstat -c | less
                    : display status 'c'ontinuously (refreshed)
                    : Ctrl+c to quit the mode
        netstat -ie : display 'e'xtend 'i'nterface (same as 'ifconfig'
                      command)
        netstat -a | grep ':80'
                    : among the output of 'netstat -a' command, filter lines 
                      that contain ':80'

* dpkg

    des. a command line tool to install, build, remove and manage Debian
         packages.

    syn. dpkg [options] action

    e.g. 
        sudo dpkg -i google-chrome-stable_current_amd64.deb
                    : install google-chrome from the Debian package 

* diff (GNU diff)

    des. compare files line by line.

    syn. diff [options] [f1] [f2]

    e.g.
        diff f1 ../f1
                    : compare f1's in two different locations line by line
                    : (in the result) the arrows ('<' and '>') refer to what
                     the value of the line is in the left('<') file, with the
                     left file being the one user entered first on the command
                     line, in this case 'f1'




===============================================================================
                             COMBINING COMMANDS 
===============================================================================

* ; (semicolon)

    e.g.
        date ; cal ; pwd
                    : execute 'date', 'cal', 'pwd' commands sequentially

    [!] Note: even if a command fails, the following commands will still be
              executed

* && (double-ampersand)

    e.g.
        ls && pwd && date && cal
                    : execute 'ls', 'pwd', 'date', 'cal' commands sequentially

    [!] Note: whenever a command fails, the following commands will NOT Be
              executed

* || (double-pipe)

    e.g.
        ls || pwd   : execute 'ls' command only            

    [!] Note: if execution of the first command is successful, the second
              command will not be executed (basically, logical OR operation)



===============================================================================
                               BASH SCRIPTING
===============================================================================

* script

    a text file that contains a sequence of command for linux based operating
    system

* creating a script

    nano myscript.sh 
        (including the extension 'sh' is not mandatory but is a good habbit)

* writing a script
    
    -----------------------------------TOP------------------------------

    #!/bin/bash         
        : location of 'bash' (use 'which bash' command to get this info) 

    /bin/ls -l
        : in linux bash script, it's a good practice to use the full path

    STRING="Hello World!"
    echo $STRING

    (more commands...)

    ---------------------------------BOTTOM-----------------------------

    [!] Note: when this script is run, a long list will be printed followed by
              the string "Hello World!"

* running a script

    ./myscript

    [!] Note: user must have the execute permission to run the scrip
    


===============================================================================
                                    .bashrc    
===============================================================================

* des. '.bashrc' file is a script that is executed whenever a new terminal
       session is started in interactive mode (resides in a user's home
       directory)

* e.g. .basrc file is used to set evironment variables 
       (setting JAVAHOME variable when installing Java) 

    [!] Note: instead of starting a new terminal session, sourcing '.bashrc'
              file will also apply the changes to the current terminal session

* opening the script

    nano .bashrc
    gedit .bashrc
    vim .bashrc 

* editing the script

    alias ls='ls --color=auto -l'



===============================================================================
                                    ELSE        
===============================================================================

## Bash Commands

* uname -a				show system and kernel
* head -n1 /etc/issue	show distribution
* mount					shot mounted filesystems
* date					show system date
* uptime				shot uptime
* whoami				show your username
* man command			show manual for 'command'


## Bash Shortcuts

* CTRL-c				stop current command
* CTRL-z				sleep program
* CTRL-a				go to start of line	
* CTRL-e				go to end of line
* CTRL-u				cut from start of line
* CTRL-k				cut to end of line
* CTRL-r				search history
* !(history#)           Repeat command with history#
* !!					Repeat last command
* !abc					run list command starting with 'abc`
* !abc:p				print last command starting with 'abc'
* !$					last argument of previous command
* !*					all arguments of previous command
* ^abc^123				run previous command, replacing 'abc' with '123'


## Bash Variables

* env					show environment variables
* echo $NAME			output value of $NAME variable
* export NAME=value		set $NAME to 'value'
* $PATH					executable search path
* $HOME					home directory
* $SHELL				current shell


## Directory Operations

* pwd					show current directory (print working directory)
* mkdir dir				make directory 'dir'
* cd dir				change directory to 'dir'
* cd ..					go up a directory
* ls					list files
	-a: show all (including hidden)
	-R: recursive list
	-r: reverse order
	-t: sort by last modified
	-S: sort by file size
	-l: long listing format
	-1: on fie per line
	-m: comma-separated output
	-Q: quoted output


## Search Files

* grep pattern files	search for 'pattern' in 'files'
* grep -i				case insensitive search
* grep -r				recursive search
* grep -v				inverted search
* find /dir/ -name name*	find files starting with 'name' in 'dir'
* find /dir/ -user name		find files owned by name in 'dir'
* find /dir/ -mmin num		find files modified less than 'num' minutes ago in 'dir'
* whereis command		find binary / source / manual for 'command'
* locate file			find 'file' (quick search of system index)


## File Operations

* touch file1			create 'file1'
* cat file1 file2		concatenate files and output
* less file1			view and paginate 'file1'
* file file1			get type of 'file1'
* cp file1 file2		copy 'file1' to 'file2'
* mv file1 file2		move 'file1' to 'file2'
* rm file1				delete 'file1'
* head file1			show first 10 lines of 'file1'
* tail file1			show last 10 lines of 'file1'
* tail -f file1			output last lines of 'file1' as it changes


## Process Management

* ps					show snapshot of processes
* top					show real time processes
* kill pid				kill process with id 'pid'
* pkill name			kill process with name 'name'
* killall name			kill all processes with names beginning 'name'


## Nano Shortcuts

* Ctrl-R				read file
* Ctrl-O				save file
* Ctrl-X				close file
* Alt-A					start marking text
* Ctrl-K				cut marked text or line
* Ctrl-U				paste text
* Alt-/					end of file
* Ctrl-A				beginning of line
* Ctrl-E				end of line
* Ctrl-C				show line number
* Ctrl-_				Go to line number
* Ctrl-W				find
* Alt-W					find next
* Ctrl-\				search and replace


## Screen Shortcuts

* screen				start a screen session
* screen -r				resume a screen session
* screen -list			show your current screen sessions
* Ctrl-A				activate commands for screen
* Ctrl-A c				create a new instance of terminal
* Ctrl-A n				go to the next instance of terminal
* Ctrl-A p				go to the previous instance of terminal
* Ctrl-A "				show current instances of terminals
* Ctrl-A A				rename the current instance of terminal


## File Permissions

* chmod 775	file		change mode of 'file' to 775
* chmod -R 600 folder	recursively chmod 'folder' to 600
* chown user:group file		change 'file' owner to user and group to group


## File Permission Numbers

* 1st diigt: owner permission
* 2nd digit: group permission
* 3rd digit: everyone permission
* Calculate each of the three permission digits by adding the numeric values of the permissions below.
	- 4: read(r)
	- 2: write(w)
	- 1: execute(x)



==============================================================================
                                 REFERENCE
==============================================================================

* Linux Command Line Tutorial For Beginners | Bash Terminal | Linux Terminal
    - https://www.youtube.com/playlist?list=PLS1QulWo1RIb9WVQGJ_vh-RQusbZgO_As
```
