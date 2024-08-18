[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > Vim Cheat Sheet

# Vim Cheat Sheet



## Global

```plain
* :help keyword - open help for keyword
* :saveas file - save file as
* :close - close current pane
* K - open man page for word under the cursor
* Ctrl+g - show file name and relevant information
* :w newfile.txt.backup - create a backup file (still working in newfile.txt)
* :w newname.txt - write to a different file with newname
* :r file.txt - read in contents in file.txt and put it to cursor position
* :version - show the current vim version (:ver)
* :colorscheme - show the current vim colorscheme
               - :colorscheme+Tab - loop through selectable colorschemes
```



## Syntax

```plain
[number] verb [number] <noun>

* d2w : [1 time] delete [2] <words>
* 2dw : [2 times] delete [1] <word>
* 2d3l : [2 times] delete [3] <characters to the right>
* 3d2l : [3 times] delete [2] <characters to the right>
* d6j : [1 time] delete [6] <lines>
* >l : [1 time] indent [current line]
* >h : [1 time] indent [current line]
* >j : indent current line and line below
* >> : indent current line
* 2>> : indent 2 lines

[!] Note: above mentioned '> (indent)' logic applies to '< (outdent)' as well
```



## Cursor Movement

```plain
* h - move cursor left (2h - move left two characters)
* j - move cursor down (2j - move down 2 lines)
* k - move cursor up
* l - move cursor right
* H - move to top of screen (High)
* M - move to middle of screen (Middle)
* L - move to bottom of screen (Low)
* w - jump forwards to the start of a word
* W - jump forwards to the start of a word (words can contain punctuation)
* e - jump forwards to the end of a word
* E - jump forwards to the end of a word (words can contain punctuation)
* b - jump backwards to the start of a word
* B - jump backwards to the start of a word (words can contain punctuation)
* ge - jump backwards to the end of a word
* % - move to matching character (default supported pairs: '()', '{}', '[]' -
      use ':h matchpairs' in vim for more info)
* 0 - jump to the start of the line
* ^ - jump to the first non-blank character of the line
* Home - jump to the start of the line
* $ - jump to the end of the line
* g_ - jump to the last non-blank character of the line
* End - jumb to the end of the line
* gg - go to the first line of the document
* G - go to the last line of the document
* Ctrl+Home - go to the first line of the document
* Ctrl+End - go to the last line of the document
* 5G - go to line 5
* :5 - go to line 5
* fx - FIND the next ocuerrence of character x
* Fx - FIND the previous occurence of character x
* tx - jump TO before next occurrence of character x
* Tx - jump TO after previous occuerence of character x
* ; - repeat previous f, t, F or T movement
* , - repeat previous f, t, F or T movement, backwards
* } - jump to next paragraph (or function/block, when editing code)
* { - jump to previous paragraph (or function/block, when editing code)
* Ctrl+e - move screen down on line (without moving cursor)
* Ctrl+y - move screen up one line (without moving cursor)   
* Ctrl+b - move back one page (full screen)
* Ctrl+f - move forward one page (full screen)
* 3+PgUp - go up 3 pages
* 3+PgDn - go down 3 pages
* Ctrl+d - move forward 1/2 a screen
* Ctrl+u - move back 1/2 a screen
* :set scrolloff=5 - set scroll offset to 5 (lines) - default is 'scrolloff=0'
* :set scrolloff=999 - set scroll offset to the middle of the screen
* 50% - move from the top down to the 50% into the file
* zt - take the current line to the top of the screen
* zz - take the current line to the center of the screen
* zb - take the current line to the bottom of the screen
* g; - go to the last (previous) change
* g, - go to the next (more recent) change
* :changes - show all changes made
* Ctrl+o - go to the previous jump
* Ctrl+i - go to the next more recent jump
* :jumps - show all jumps (searches, substitutes, finding marks...) 
         - [!] Note: scrolling through the file, paging or moving cursor
           up/down are NOT jumps
```



## Editing

### Insert Mode - Inserting/Appending Text

```plain
* i - insert before the cursor
* I - insert at the beginning of the line
* a - insert (append) after the cursor
* A - insert (append) at the end of the line
* o - append (open) a new line below the current line
* O - append (open) a new line above the current line
* ea - insert (append) at the end of the word
* Esc - exit insert mode
```

### Editing in Normal Mode

```plain
* r - replace a single character
* J - join line below to the current one without space in between
* gJ - join line below to the current one with a space in between
* gwip - reflow paragraph
* cc - change (replace) entire line
* C - change (replace) to the end of the line (D+i)
* C$ - change (replace) to the end of the line
* ciw - change (replace) entire word
      - the cut word replaces whatever there  was in the default register
* cw - change (replace) to the end of the word
* s - delete character and substitue text
* S - delete line and substitute text (same as cc)
* xp - transpose two letters (delete and paste)
* u - undo
* Ctrl+r - redo
* . - repeat last command
* R - enter replace(overwrite text) mode ('backspace' to undo)
* J - join the line below to the current line (J = 1J = 2J) 
* 3J - join next 2 lines to the current line (4J - ... next 3 lines...)
* :10,19sort - sort lines from line 10 to 19
* :10,19sort! - sort lines from line 10 to 19 in reverse order
* %sort - sort all lines in the current file
* %sort! -  sort all lines in the current file in reverse order
* :set list - show 'end of line ($)' characters 
```

### Numbers

```plain
* Ctrl+a - increment the number by 1
         - this works as long as the cursor is on the left to the number
* 5+Ctrl+a - repeat 'Ctrl+a' 5 times (increment the number by 5)
* Ctrl+x - decrement the number by 1
         - this works as long as the cursor is on the left to the number
* g+Ctrl+a - set array indecies in increasing order

        myArray[0] = 0;                              myArray[0] = 0;
        myArray[0] = 0;  <--- select from here       myArray[1] = 0;
        myArray[0] = 0;                              myArray[2] = 0;
        myArray[0] = 0;                              myArray[3] = 0;
        myArray[0] = 0;                              myArray[4] = 0;
        myArray[0] = 0;         ---------->          myArray[5] = 0;
        myArray[0] = 0;                              myArray[6] = 0;
        myArray[0] = 0;                              myArray[7] = 0;
        myArray[0] = 0;                              myArray[8] = 0;
        myArray[0] = 0;                              myArray[9] = 0;
        myArray[0] = 0; <--- to here                 myArray[10] = 0;
 
* :put =range(10,15) - put range of numbers vertically from the next line of
                       current cursor position
* :12put =range(10,15) - put range of numbers vertically from the next line of
                       user specified line 12 (which is line 13)
* :0put =range(10,15) - put rnage of numbers vertically from the beginning of
                        the file
* :$put =range(10,15) - put rnage of numbers vertically at the bottom of the
                        file
* :for i in range(115,120) | put = '192.168.0.'.i | endfor
                    - putting sequential IP addresses using for loop
                    - .i means put in the range user specified
* :%s/^/\=print('%-4d', line('.')) - insert line numbers in the entire file
                                     (numbers are left justified)
                                   - 's' means substitute
                                   - '^' means beginning of the file
* :%s/^/\=print('%4d', line('.')) - insert numbers right justified
                                  - '%4-d' -> '%4d'
* :%s/^/\=print('%04d', line('.')) - insert numbers right justified and zero
                                     filled (leading zeros)
```

### Cut and Paste

```plain
* yy - yank (copy) a line (and save it in memory)
* Y - yank (copy) a line
* 2yy - yank (copy) 2 lines
* y2w - yank (copy) 2 words
* yw - yank (copy) the characters of the word from the cursor position to the
       start of the next word
* yi{ - yank (copy) in curly braces
* y$ - yank (copy) to end of line
* p - put (paste) the clipboard after cursor
* 2p - put (paste) the clipboard after cursor twice
* P - put (paste) before cursor	
* Ctrl+r " - paste from default register (in insert mode) 
* dd - delete (cut) a line
* 2dd - delete (cut) 2 lines
* dw - delete (cut) the characters of the word from the cursor position to
       the start of the next word
* D - delete (cut) from the cursor to the end of the line
* 2D - delete to end of line and the next line
* dip - delete in paragraph 
* d$ - delete (cut) to the end of the line
* dt. - delete TO the '.'
* d/pattern - delete from the cursor to pattern
* d?pattern - delete from the cursor to pattern backwards (including pattern)
* x - delete (cut) character
* :set Paste - pastes text in alignment(not needed on updated versions of vim)
```

### Search and Replace

```plain
* * - search forwarad for current pattern
* # - search backward for current pattern
* /pattern - search for pattern
* /\cpattern - search for pattern case insensitive
* /\Cpattern - search for pattern case sensitive
* /\<pattern\> - search for independent pattern (surrounded by spaces)
* /\/\* - search for '/*' (escaping special characters)
* ?pattern - search backward for pattern
* ?\cpattern - search backward for pattern case insensitive
* ?\Cpattern - search backward for pattern case sensitive
* \vpattern - 'very magic' pattern: non-alphanumeric characters are interpreted
              as special regex symbols (no escaping needed)
* n - repeat search in same direction
* N - repeat search in opposite direction
* :4,7s/old/new - replace all old with new from line 4 to 7
* :%s/old/new - replace all old with new throughout(%s) file
* :%s/\<old\>/new - replace all independent old with new throughout(%s) file
* :%s/old/new/g - ... globally
* :%s/old/new/gc - ...globally with confirmations (see confirmation msg below)
      - replace with new (y/n/a/q/l/^E/^Y)?
        y - yes
        n - no
        a - all after current cursor position
        q - quit
        l - make this the last change and quit (yq) 
        ^E - scroll down until current cursor position hits top of screen 
        ^Y - scroll up until current cursor position hits bottom of screen 
* :%s/old/new/gic - ...globally with confirmations case insensitive
* :%s/old/new/gIc - ...globally with confirmations case sensitive
* :set hlsearch - turn on search highlighting (:set hl)
* :set nohlsearch - turn off search highlighing (:set nohl, :set noh)
* :set incsearch - turn on incrememtal search highlighting
* :set noincsearch - turn off incrememtal search highlighting
* /[arrow up] - search through forward search history
* ?[arrow up] - search through backward search history
* :[arrow up] - search through command history
* q/ - show forward search history (records are editable)
* q? - show backward search history (records are editable)
* q: - show command history (records are editable)
```

### Visual Mode

```plain
1. ENTER VISUAL MODE
====================

* Ctrl+v  start visual mode, mark lines, then do a command (like y-yank)
* V       start linewise visual mode

2. SELECT AREA
==============

* $ - select to the end of the line
* aw - mark a word
* ab - a block with ()
* aB - a block with {}
* iw - select current word under cursor
* i+parenthesis - select everything inside parenthesis (e.g. vi(, vi{, vi[)
* iB - select everything inside block (curly braces), same as vi{
* ib - inner block with ()
* iB - inner block with {}

3. TAKE ACTION
==============

* o - move to other end of marked area
* I - insert text to the left of visual block
* A - append to the right of the visual block
* c - change visual block (delete the block and insert new text to the block)
* d - delete visual block 
* 0 - move to other corner of block
* > - shift text right
* < - shift text left
* y - yank (copy) marked text
* d - delete marked text
* ~ - switch case
* w - mark to before the start of next word
* e - mark to the end of a word
* fed - find and mark to the letter 'e' and delete the marked text
* fey - find and mark to the letter 'e' and yank the marked text
* Esc - exit visual mode
```



## Tabs and Indents

```plain
* :set list - show invisible characters (^I: Tab, $: end of line)
* :set expandtab - set to insert spaces instead of tab character
                 - ':set noexpandtab' to unset
* :set shiftwidth - show current shiftwidth value
* :set shiftwidth=4 - set shift width to 4 (:set sw=4)
* :set tabstop - show current tabstop value
* :set tabstop=4 - set tabstop to 4 (:set ts=4)
                 - if tabstop=8 and shiftwidth=4, tab character will appear
                   when shift happens twice
* :set softtabstop - show current shofttabstop value
                   - ':set softtabstop=0' use tabstop value as a default
* :filetype - check to see if vim is detecting file types
            - [!] Note: for filetype indent setting, see '.vimrc' section
```

### In Normal Mode

```plain
* >> - indent a single line
* << - outdent a single line
* 4>> - indent the current line and the 3lines below it
* 4<< - outdent the current line and the 3lines below it
* 14== - auto-format 14 lines from the current line
[!] Note: ':e $VIMRUNTIME/indent' to see the file vim refers to for filetype
          auto-formatting information (depending on the version, this path may
          point to a different location
```

### In Insert Mode

```plain
* Tab - indent
* Ctrl+v u0009 - insert a tab character
* Ctrl+v Ctrl+i - insert a tab character
* Backspace - outdent
[!] Note: to change tab character to a single character, see '.vimrc' section
```

### In Visual Mode

```plain
* (visually selected block) > - indent selected block
* (visually selected block) = - auto-foramt (auto indent)
```



## Registers

```plain
little storage places where you can yank text and keep it for later
(registers we can yank in to are: a - z)

* :register - show registers content (:reg)
            - ": - special register for the last command user typed
            - "/ - special register for the path directory
            - "% - special register for the document name 
            - ". - register for last command
            - "0 - last yanked register
                 - when an entire line is yanked, '^j' character is appended at
                   the end of the yanked line in the register
            - "" - default register
            - "- - register for the last deleted text (dash/hyphen register)
                 - when an entire line is deleted, it is saved in both '""' and
                   '"1' registers. numbered registers will keep bumped down and
                   down until it gets to '"9' (there's no '"10' register so
                   after '"9' register, it will be lost)
            - "a - user specified register 'a'
            [!] Note: when we yank text, it is saved in both '"0' and '""'
* "/p  a  Ctrl+r %  Ctrl+r : - print 'dir', 'doc name', 'last command'
* "ay - yank into register a (each letter on the keyboard corresponds to
        different registers)
* "ayiw - yank in word to register 'a' (as well as to the default register)
* "ap - paste contents of register 'a'
* Ctrl+r " - paste from default register (in insert mode) 
* Ctrl+r a - paste contents of register 'a' (in insert mode) 
* Ctrl+r Ay - append and yank into register 'a'
* ciw - change (replace) entire word
      - the cut word replaces whatever there  was in the default register
      - 'Ctrl+r a' will replace the original word with the contents of reg 'a' 
* to clear all the registers???
[!] Note: registers are being stored in ~/.viminfo, and will be loaded again on
          next restart of vim.
[!] Note: Register 0 contains always the value of the last yank command. 
```



## Marks

```plain
bookmarks

* :marks - list of marks
* ma - set current position for mark 'a'
* `a - jump to the position of mark 'a' in the line marked
* 'a - jump to line with mark 'a' (NOT to the position where mark was placed)
* y`a - yank text to position of mark 'a'
* [' - jump to the previous mark line
* ]' - jump to the next mark line
* [` - jump to the previous mark
* ]` - jump to the next mark
* d'a - delete to the line with mark 'a'
* y'a - yank (copy) from cursor to the mark 'a' (then bring cursor to the top
        of the selection / '] to go to the bottom of the selection)
* mA - set current position for mark 'A' (uppercase works the same way as the
       lower case works as long as you are in the same file, mark with
       uppercase can be referenced from a different file)
* (in another file) 'A - open file and go to the mark 'A'
* :delmarks a,A,C - delete marks 'a', 'A', 'C'
* :delmarks A-Z - delete marks from 'A' to 'Z'
* :delmarks! - delete all marks
```



## Macros

```plain
vim macro feature allows you to record and play a sequence of actions inside of
the vim editor (macros are stored in registers a-z)

* qa - start recording a macro into the register 'a'
* q - stop recording macro
* @a - run macro stored in the register 'a'
* 3@a - run macro stored in the register 'a' 3 times
* @@ - return last run macro
* :let @b='05^Xj' - setting '5Ctrl+x' to macro(register) 'b'
```

### Exiting

```plain
* :w - write (save) the file, but don't exit
* :w !sudo tee % - write out the current file using sudo
* :wq - write (save) and quit (:x, ZZ)
* :q - quit (fails if there are unsaved changes)
* :q! - quit and throw away unsaved changes (ZQ)
* wqa - write (save) and quit on all tabs
```



## Working with Multiple Files

```plain

* :vimgrep /pattern/ {`{file}`} - search for pattern in multiple files 
                                  (e.g. :vimrgrep /foo/ ??/?)
* cn - jump to the next match
* cp - jump to the previous match
* copen - open a window containing the list of matches
* :e file - edit a file in a new buffer
* :bnext - go to the next buffer (:bn)
* :bprev - go to the previous buffer (:bp)
* :bd - delete a buffer (close a file)
* :ls - list all open buffers
* :r file - read file content (insert text content of file below the cursor)
```

### Split Windows

```plain
* :split - split window horizontally (:new, :sp)
* :vsplit - split window vertically (:vnew, :vsp)
* :sp file - open a file in a new buffer and split window
* :vsp file - open a file in a new buffer and vertically split window
* :set splitright - set to make the split show up on the right
* :enew - open new file in the current window
* Ctrl+w s - split window
* Ctrl+w v - split window vertically
* Ctrl+w w - switch windows
* Ctrl+w x - swap window position
* Ctrl+w r - rotate windows
* Ctrl+w R - rotate windows in opposite direction
* Ctrl+w t - go to the top left window
* Ctrl+w b - go to the bottom right window
* Ctrl+w p - go to the previous window
* Ctrl+w o - show only the active window (others still remain in buffer)
* :ba - bring all buffers to split windows (horizontal)
* :vert ba - bring all buffers to split windows (vertical)
* :vert 3ba - show all buffers up to buffer 3 (vertical)
* :vsp | b3 - split buffer 3 (vertical)
            - if '|' is omitted, vim will try to split and open a file named b3
* Ctrl+w q - quit a window
* Ctrl+w h - move cursor to the left window (vertical split)
* Ctrl+w l - move cursor to the right window (vertical split)
* Ctrl+w j - move cursor to the window below (horizontal split)
* Ctrl+w k - move cursor to the window above (horizontal split)
* :resize 20 - resize the current window to 20 (horizontal)
* :resize -5 - downsize 5 the current window (horizontal)
* :vertical resize 80 - resize thecurrent window to 80 (vertical)
* :vertical resize +25 - resize +25 the current window (vertical)
* Ctrl+w = - make all windows the same size
* Ctrl+w _ - max current window height
* Ctrl+w | - max current window width
* :help ctrl-w - show everything you can do with 'Ctrl+w'
```

### Buffers

```plain
piece of memory in RAM

* :buffers - show all buffer info (:ls)
    - %a    currently active buffer
    - #     alternate buffer (where you just came from)
    - h     hidden buffer
    - +     buffer has unsaved changes
* :e filename.txt - open up 'filename.txt' (will be added to 'buffers' list)
* :bp - buffer previous (':bp!' force command)
* :bn - buffer next
* :set hidden - hide buffer (hidden setting applies to all files in buffer)
              - ':set nohidden' to unset
* b4 - jump to buffer 4
* Ctrl+6 - toggle back and forth between the current and alternate buffers
* Ctrl+^ - toggle back and forth between the current and alternate buffers
* :bd - delete this buffer
* e! - remove all the changes since the last save
```



## Window Tabs

```plain
* :tabeddit filename.txt - open and eddit a file in a new tab (:tabe)
                         - same file can be opened in multiple tabs
* :tabnew or :tabnew {page.words.file} - open a file in a new tab
* :tabnext - go to the next tab (to the right and loop around) (:tabn, :gt)
* :3gt - go 3 tabs right
* Ctrl+PgUp - go to the next tab
* :tabnext 3 - go to the right 3 tabs
* :tabprev - go to the previous tab (to the left and loop around) (:tabp, :gT)
* :3gT - go 3 tabs left
* Ctrl+PgDn - go to tne previous tab
* Ctrl+wT - move the current split window into its own tab
* :tab split - split a window into a new tab (without removing the original)
* :tabmove # - move current tab to the #th position (indexed from 0) (:tabm #)
* :tabm - move current tab to the far right
* :tab 0 - move current tab to the far left
* :w newname.txt - name the file and save 
* :tabclose - close the current tab and all its windows (:tabc)
            - but they still remain in buffer
* :tabclose 3 - close the tab 3
* :tab ball - open all buffers each in its own tab
* :set tabpagemax=9 - set tab page max value to 9
* :tabonly - close all tabs except for the current one (:tabo)
* :drop file.txt - drop the current file and open file.txt in the current tab 
                 - if file.txt is already open in one of the tabs, current file
                   does not drop and it just goes to the file.txt tab
* :tabdo command - run the command on all tabs (e.g. :tabdo q - closes all
                   opened tabs)
[!] Note: when a tab contains multiple split windows, the tab name will change
          depending on selected window
```



## Text Appearance

```plain
* :set wrap - word wrap
* :set linebreak - break lines at words
* :set cursorline - highlight current line
* :set colorcolumn=x - show vertical line where 'x' is the column number
* :set list - show all whitespaces
* :set textwidth=x - automatic word wrapping (text wraps automatically as you
  type, in order to make each line long 'x' characters at most)
* :set number (:set nu)
* :set nonumber (:set nonu)
* :set relativenumber (:set rnu)
* :set norelativenumber(:set nornu)
```

### Folding Text

```plain
the way of hiding text from view

* :set foldmethod - show current foldmethod (:set fdm)
                  - 'manual' is default and most popular (folds must be defined
                    by entering commands; like 'zf')
                  - 'indent' creates a fold for each indent
                  - 'syntax' creates a fold by the syntax of a programming
                    language 
                  - 'expr' folds are created by a user-defined expression (like
                    a regex / regular expression)
                  - 'marker' creates folds for start and end characters a user
                    defines
                  - 'diff' is used to fold unchanged text when viewing
                    differences (automatically set in diff mode)
                    
FOLDMODE=MANUAL
===============

* (visually select text block) zf - create a fold
* zf10j - fold up current line + next 10 lines
* zf10k - fold up current line + prev 10 lines
* :148,158fold - fold the designated scope of lines
                 (:148,158 fold, :148,158 fo) 
* :,+10 fo - fold current line + next 10 lines
* :,-10 fo - fold current line + prev 10 lines
* :zfa{ - create a fold between { and } (line wise)
* zo - open up a fold
* zO - open the fold and all the nested folds
* zn - open up all folds
* :86, 136 foldopen - open up folds located in lines 86 ~ 136
                    - to retrieve the last line of the file 'Ctrl+g'
* :86, 136 foldclose - close folds located in lines 86 ~ 136
* :% foldopen - open up all the folds in current file ('%' means 'everything')
* :% foldclose - close all the folds in current file ('%' means 'everything')
* zN - set all folds as they were before executing zn command
* zj - go to the next fold
* zk - go to the prev fold (last line of the previous fold)
* [z - go to the top of the current fold
* ]z - go to the bottom of the current fold
* zc - close fold
* zd - delete fold
* zC - close this fold and all folds it is nested in
* zA - toggle between zC and zO
* zE - eliminate all folds from the curent file
* :mkview - save a fold (saved folds goes to '~/.vim/vim')
          - vim does not remember unsaved folds
* :loadview - load the saved views
            - saved views must be loaded to show up
[!] Note: make this process easier by adding commands to .vimrc


FOLDMODE=INDENT
===============

* :set fdm=indent - fold up all indented texts
* zr - reduce all folds one level (same as 'zo' for all the nested folds)
* zR - reduce all folds in the file
* zm - fold up all folds one level (same as 'zc' for all folds)
* zM - fold up all folds all the way (same as 'zC' for all folds)
```



## Navigating Filesystem

### NetRW Plugin

Built in vim v7.0 or higher

```plain
* vim [directory] - open the directory with vim's built in file explorer
                    plugin('NetRW') and show what is in that directory
* vim . - go to the file explorer in the current directory
        - vim's file explorer shows hidden files by default (can hide them)
* /[file/directory] - find file or directory in vim's file explorer
* - - go up one directory level (or hit enter on '../')
* :q - quit out of the file explorer
* D - delete a file or directory with confirmation
* R - rename a file or directory
* s - sort files or directories
* x - open the file with linux's default text editor
* % - create a file
* d - crate a directory
* gh - hide hidden files ('Hiding' will appear on the header)
     - 'gh' again will toggle the hidden files back on
* I - hide the header
    - 'I' again will toggle the header back on
* o - open the file in a split window (new split window is placed on the top,
      cursor is in that window)
* v - open the file in a vertial split window (new split window is placed on
      the left, cursor is in that window)
      that window)
* :let g:netrw altv=1 - set to open the split to the right
* p - preview the file (cursor stays on explorer window, 'Ctrl+w w' to go to
      the preview window)
* Vex - split back into the vertical explorer window
* Sex - split back into the horizontal explorer window

HEADER INFO
===========

* Netwr Directory Listing (netrw v156)
    - NetRW plugin version info
* /home/kyungjae-lee/linux 
    - current working directory
* Sorted by 
    - how the directory is sorted (hit 'enter' on the line or hit 's' anywhere
      in the file explorer to change)
        - name
        - size
        - exten(sion)
* Sort sequence
    - hit 'enter' on the line or hit 'S' anywhere in the file explorer to edit
      the sequence
* Quick Help
    - hit 'enter' on the line and the quick help menu will loop around
    - also can enter Quick Help by executing ':help quickhelp'

COPY and MOVE
=============
copy a file or group of files from the current directory to another

1. mark the target directory
    - put cursor to the target directory-to-bo and
    - mt
    - 'Copy/Move Tgt:' will newly appear on the file explorer header

2. mark a file or files to copy
    - mf (make sure the file name is bold or highlighted)

3. copy the files
    - mc (if successful, prviously highlighted file names will no longer be
      highlighted)

NetRW FIX FUZZY FIND
====================
* vim - enter vim

* r filename.txt - read in filename.txt
* e filename.txt - edit a filename.txt
* :find filename.txt - find a 'filename.txt' (subfolder that the target file is
                       located must be specified! to fix this see ':set path')
* :set path - show fuzzy search path (to make NetRW search all subdirectories and
              recursively, see '.vimrc' section)
            - ',' is a separator
* (after editing the 'path') find filename.txt - search filename.txt through
            all paths, spoting one by one as user hits 'Tab' key
* (when 'wildmenu' is set) find filename.txt + Enter - enter the first file
            that matches.
* (when 'wildmenu' is set) find filename.txt + Tab - loop though all the files
            that match. hit 'Enter' key to enter the target file.
```



## Vimgrep

```plain
vim's built-in command for searching across multiple files

* :vimgrep /pattern/g % - search 'pattern' only in this open buffer(%)
                          (:vim /pattern/g %)
* :vimgrep /pattern/g **/* - search 'pattern' through the entire project dir.
                           - '.vimrc' must be updated for this to work. (see
                             'set path' in '.vimrc' section)
* :cope - (c open) show search result as a list (j, k to navigte, 'Enter' to go
          to the occurrence) (:cw)
* :cnext - go to the next match
* :cprev - go to the previous match
* :cfirst - go to the first match
* :clast - go to the last match
```



## Help

```plain
* :help - to the main 'help' screen
* :help [command] - to the '[command]' section of the 'help' screen
```



## Plugins

```plain
download plugins under the path '~/.vim/pluginname' 
    - make '~/.vim' directory if you don't have one
    - it's good to make designated 'pluginname' directory for each plugin
      (as long as '.vimrc' points to the right directory, there won't be any
      problems running plugins)
```

### Example (Color Scheme Plugin)

```plain
1. download 'badwikf-master.zip' from http://github.com/sjl/badwolf
2. mkdir ~/.vim/color 
    - 'color' is user designated plugin directory name
3. cd ~/.vim/color
4. mv ~/Downloads/badwolf-master.zip .
5. unzip badwolf-master.zip
6. ls -lah
    - check if the unzip was successfully done
7. cd ~
8. vim .vimrc
    - set runtimepath^=~/.vim/color/badwolf-master
9. :wq vim and re-enter so that vim applies the new updates
10. :colorscheme badwolf
    - badwolf color scheme is working!
11. to undo this color scheme change, comment out '9.', :wq and re-enter vim
```

### Vim Plugin Manager

```plain
https://vimawesome.com
some examples of plugin manager
    - Vundle
    - NeoBundle
    - VimPlug
    - Pathogen

VimPlug Manager
~~~~~~~~~~~~~~~

1. download VimPlug manager from https://github.com/junegunn/vim-plug
2. mkdir ~/.vim/autoload
    - 'autoload' is a special directory for vim. it's meant to delay loading
      the code until it's actually needed. this is to keep vim running fast.
3. cd .vim/autoload
4. mv ~/Downloads/vim-plug-master.zip .
5. ls -lah
6. unzip vim-plug-master.zip
7. mv vim-plug-master/plug.vim . 
8. cd (cd~)
9. vim .vimrc
    - call plug#begin('~/.vim/plugged')
        - 'call' is telling vim to look in the .vim/autoload directory for the
          .vim file called 'plug'
        - '~/.vim/plugged' does not exist yet but vim will take care of it and
          this is where VimPlug will place the installed plugins
    - call plug#end()
10. go to https://vimawesome.com
    - go to Syntastic plugin
    - go to VimPlug
    - copy 'Plug ...' into .vimrc (between 'call plug#begin' and 'plug#end')
11. add a few more lines to .vimrc for Perl script (it is found that without
    following lines, perl script errors were not being detected correctly)
    - let g:syntastic_check_on_open         = 1
    - let g:syntastic_check_on_wq           = 0
    - let g:syntastic_enable_perl_checker   = 1
    - let g:syntastic_perl_checkers         = [perl', 'podchecker']
    [!] Note: above lines of code simply means turning on the perl checker and
    telling syntastic what checker user wants to use
11. source .vimrc
    - it means to :wq and enter back in the .vimrc
12. :PlugInstall
    - install the plugin
13. quit out of plugins window, quit out of .vimrc

[!] Note: to clear the plugin, 
    - go to .vimrc and comment out everything but the ones we wrote at '9.'
    - :PlugClean 
        - this actually removes the plugin with confirmation
```



## Vim Diff

```plain
vim's file compare feature that allows users to see the difference between 2 or
3 files

* vimdiff file1.txt file2.txt 
    - open 'file1' to the left, 'file2' to the right
    - highlight the differences between the two
* :quitall - quit from both of the files (:qa)
* vimdiff -o file1.txt file2.txt 
    - open 'file1' on the top, 'file2' on the bottom
      (open one 'o'ver the other)
* vim -d -o file1.txt file2.txt 
    - same as 'vimdiff -o file1.txt file2.txt'
    - same as 'vim -do file.txt file2.txt'
* :set noscrollbind
    - the file with this command ran scrolls independently
    - when the cursor is on the other file where this command has not yet been
      run, both sides will scroll together
* ]c - jump to the next change(difference)
* [c - jump to the previous change(difference)
* (cursor on the left file) :diffget
    - :do - meaning diff obtain
    - bring the line on the file right into the file on the left
* (cursor on the left file) :diffput
    - :dp - meaning diff put
    - bring the line on the file left into the file on the right
* (cursor on the file that the change was made to) u - undo
    - notice that when you undo ':diffget' that specific difference is not
      flagged anymore!
    - rescanning needs to be done to detect it again!
* :diffupdate
    - rescan the difference
```



## .vimrc (Vim Run Command)

```plain
* " - comment out
* set nu
	- set number (can be toggled by entering ':set nu!' multiple times)
* set nonu
	- set nonumber

MAPPING
=======

* nmap <F3> :set nu! <CR> 
	- first 'n' stands for 'normal mode'
	- map F3 to toggle on and off the line numbers in normal mode
	- if there happen to be space after <CR>, cursor will move everytime the
	  comand is executed.
* let mapleader = ','
	- map <leader> key to comma
	- unless otherwise specified, default <leader> is '\'
* nmap <leader><F3> :set rnu! <CR>
	- map <leader>F3 to toggle on and off the 'relative line numbers in normal
	  mode
* imap <F3> <ESC> :set nu! <CR>i
	- first 'i' stands for 'insert mode'
	- map F3 to toggle on and off the line numbers in insert mode
		- it has to go to the 'normal mode' first and then return to 'insert
		  mode' at the end
* imap <F4> <ESC> :set rnu! <CR>i
	- map F4 to toggle on and off the 'relative line numbers in insert
	  mode

Re Assign Arrow Keys to No OPeration (in insert mode too)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
You can set a key to no operation!
(no remap means the keys are not allowed to be mapped afterwards)

* nnoremap <Up> <Nop>
* nnoremap <Down> <Nop>
* nnoremap <Left> <Nop>
* nnoremap <Right> <Nop>
* inoremap <Up> <Nop>
* inoremap <Down> <Nop>
* inoremap <Left> <Nop>
* inoremap <Right> <Nop>

AUTOCORRECT DICTIONARY
======================

* abbr ture true
* abbr flase false
* abbr emailF kjxlee@gmail.com

SET DEFAULT SEARCH HIGHLIGHTING ON
==================================

* set hlsearch (to set off ':set nohsearch')
* set incsearch (to set off ':set noincsearch')

UPDATING TAB AND END OF LINE CHARACTER
======================================

* set listchars=tab:→\ , eol:↲
    - update the tab character to '→ unicode u2192'
    - update the end of line character to '↲ unicode u21b2'
    
FILETYPE INDENT
===============

* filetype indent on
    - turn on filetype indent
* autocmd Filetype perl setlocal noexpandtab tabstop=4 sw=4 sts=4
    - change tab settings for perl programs

TEXT FOLDING
============
lines to save text folding

* autocmd BufWinLeave *.* mkview
* autocmd BufWinEnter *.* loadview

NETRW SETTING
=============

* nmap <F4> gh
    - map F5 to toggle on and off the line numbers in normal mode

* let ghregex='\(^\|\s\S\)\zs\.\S\+'
  let g:netrw_list_hide=ghregex
  - hide the dot files one startup

* nmap <F6> I
    - map F6 to toggle on and off the header (banner)

* let g:netrw_banner=0
    - turn off the header at the top of the screen on startup

* let g:netrw liststyle= 0  
    - default view (directory name / file name)

* let g:netrw liststyle= 1  
    - show time and size

* let g:netrw liststyle= 2  
    - show listing in 2 columns

* let g:netrw liststyle= 3  
    - show the tree listing

* let g:netrw_winsize=0
    - set default window size to be always equal

* let g:netrw_preview=1
    - open splits to the right

FIX FUZZY FIND
~~~~~~~~~~~~~~

* autocmd FileType netrw setl bufhidden=delete (or use ':qa!')
    - per default, netrw leaves unmodified buffers open. this autocommand
      deletes netrw's buffer once it's hidden (using ':q;, for example)
* set nocompatible - limit search to your project
* set path+=** - search all subdirectories and recursively
* set wildmenu - shows multiple matches on one line

COLOR SHCEME
============
see 'PLUGIN' -> 'EXAMPLE' section!

* colorscheme pablo
    - use pablo colorscheme

TEXTWRAP
========
* set textwidth=80

[!] Note: if this command doesn't seemt to work, 

1. check 'formatoptions' option. it requires 't' (Vim default: tcq)
2. check if 'paste' option is switched on (to switch it off 'set nopaste')
    - when the 'paste' option is switched on (also when it was already on):
                - mapping in Insert mode and Command-line mode is disabled
                - abbreviations are disabled
                - 'autoindent' is reset
                - 'expandtab' is reset
                - 'formatoptions' is used like it is empty
                - 'revins' is reset
                - 'ruler' is reset
                - 'showmatch' is reset
                - 'smartindent' is reset
                - 'smarttab' is reset
                - 'softtabstop' is set to 0
                - 'textwidth' is set to 0
                - 'wrapmargin' is set to 0
    These options keep their value, but their effect is disabled:
                - 'cindent'
                - 'indentexpr'
                - 'lisp'
3. run ':verbose set fo' to check which file the 'formatoptions' option is
   most recently set from. 
    - remove the cause of unintentional behavior and set 'formatoptions=tcq' if
      necessary in your .vimrc file.
```
