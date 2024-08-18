[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > Tmux Cheat Sheet

# Tmux Cheat Sheet



## Sessions

* Start a new session

  ```plain
  tmux
  ```

  ```plain
  tmux new
  ```

  ```plain
  tmux new-session
  ```

  ```plain
  :new
  ```

* Start a new session with a name

  ```plain
  tmux new -s <session_name>
  ```

  ```plain
  :new -s <session_name>
  ```

* Kill/delete session

  ```plain
  tmux kill-ses -t <session_name>
  ```

  ```plain
  tmux kill-session -t <session_name>
  ```

* Kill/delete all sessions but the current

  ```plain
  tmux kill-session -a
  ```

* Kill/delete all sessions but the current

  ```plain
  tmux kill-session -a -t <session_name>
  ```

* Rename session

  `Ctrl` + `b` `$`

* Detach from session

  `Ctrl` + `b` `d`

* Detach others on the session (Maximize window by detach other clients)

  ```plain
  :attach -d
  ```

* Show all sessions

  ```plain
  tmux ls
  ```

  ```plain
  tmux list-sessions
  ```

  `Ctrl` + `b` `s`

* Attach to last session

  ```plain
  tmux a
  ```

  ```plain
  tmux at
  ```

  ```plain
  tmux attach
  ```

  ```plain
  tmux attach-session
  ```

* Attach to a session with a name

  ```plain
  tmux a -t <session_name>
  ```

  ```plain
  tmux at -t <session_name>
  ```

  ```plain
  tmux attach -t <session_name>
  ```

  ```plain
  tmux attach-session -t <session_name>
  ```

* Session and Window Preview

  `Ctrl` + `b` `w`

* Move to previous session

  `Ctrl` + `b` `(`

* Move to next session

  `Ctrl` + `b` `)`



## Windows

* Start a new session and window with names

  ```plain
  tmux new -s <session_name> -n <window_name>
  ```

* Create window

  `Ctrl` + `b` `c`

* Rename current window

  `Ctrl` + `b` `,`

* Close current window

  `Ctrl` + `b` `&`

* List windows

  `Ctrl` + `b` `w`

* Previous window

  `Ctrl` + `b` `p`

* Next window

  `Ctrl` + `b` `n`

* Switch/select window by number

  `Ctrl` + `b` `0`...`9`

* Toggle last active window

  `Ctrl` + `b` `l`

* Reorder window, swap window number 2(src) and 1(dst)

  ```plain
  :swap-window -s 2 -t 1
  ```

* Move current window to the left by one position

  ```plain
  :swap-window -t -1
  ```



## Panes

* Toggle last active pane

  `Ctrl` + `b` `;`

* Split pane with horizontal layout

  `Ctrl` + `b` `%`

* Split pane with vertical layout

  `Ctrl` + `b` `"`

* Move the current pane left

  `Ctrl` + `b` `{`

* Move the current pane right

  `Ctrl` + `b` `}`

* Switch to pane to the direction

  `Ctrl` + `b` `↑`

  `Ctrl` + `b` `↓`

  `Ctrl` + `b` `→`

  `Ctrl` + `b` `←`

* Toggle synchronize-panes(send command to all panes)

  ```plain
  :setw synchronize-panes
  ```

* Toggle between pane layouts

  `Ctrl` + `b` `Spacebar`

* Switch to next pane

  `Ctrl` + `b` `o`

* Show pane numbers

  `Ctrl` + `b` `q`

* Switch/select pane by number

  `Ctrl` + `b` `q` `0`...`9`

* Toggle pane zoom

  `Ctrl` + `b` `z`

* Convert pane into a window

  `Ctrl` + `b` `!`

* Resize current pane height(holding second key is optional)

  `Ctrl` + `b` `↑`

  `Ctrl` + `b` `Ctrl` `↑`

  `Ctrl` + `b` `↓`

  `Ctrl` + `b` `Ctrl` `↓`

* Resize current pane width(holding second key is optional)

  `Ctrl` + `b` `→`

  `Ctrl` + `b` `Ctrl` `→`

  `Ctrl` + `b` `←`

  `Ctrl` + `b` `Ctrl` `←`

* Equally balance split panes

  `Ctrl` + `b`, `Alt` + `1` (horizontal; equal width)
  
  `Ctrl` + `b`, `Alt` + `2` (vertical; equal height)
  
* Close current pane

  `Ctrl` + `b` `x`



## Copy Mode

* Use vi keys in buffer

  ```plain
  setw -g mode-keys vi
  ```

* Enter copy mode

  `Ctrl` + `b` `[`

* Enter copy mode and scroll one page up

  `Ctrl` + `b` `PgUp`

* Quit mode

  `q`

* Go to top line

  `g`

* Go to bottom line

  `G`

* Scroll up

  `↑`

* Scroll down

  `↓`

* Move cursor left

  `h`

* Move cursor down

  `j`

* Move cursor up

  `k`

* Move cursor right

  `l`

* Move cursor forward one word at a time

  `w`

* Move cursor backward one word at a time

  `b`

* Search forward

  `/`

* Search backward

  `?`

* Next keyword occurrence

  `n`

* Previous keyword occurrence

  `N`

* Start selection

  `Spacebar`

* Clear selection

  `Esc`

* Copy selection

  `Enter`

* Paste contents of buffer_0

  `Ctrl` + `b` `]`

* Display buffer_0 contents

  ```plain
  :show-buffer
  ```

* Copy entire visible contents of pane to a buffer

  ```plain
  :capture-pane
  ```

* Show all buffers

  ```plain
  :list-buffers
  ```

* Show all buffers and paste selected

  ```plain
  :choose-buffer
  ```

* Save buffer contents to a file

  ```plain
  :save-buffer <file>
  ```

* Delete buffer_1

  ```plain
  delete-buffer -b 1
  ```

  

## Misc

* Enter command mode

  `Ctrl` + `b` `:`

* Set OPTION for all sessions

  ```plain
  :set -g OPTION
  ```

* Set OPTION for all windows

  ```plain
  :setw -g OPTION
  ```

* Enable mouse mode

  ```plain
  :set mouse on
  ```

  

## Help

* List key bindings(shortcuts)

  ```plain
  tmux list-keys
  ```

  ```plain
  :list-keys
  ```

  `Ctrl` + `b` `?`

* Show every session, window, pane, etc...

  ```plain
  tmux info
  ```
