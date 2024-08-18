[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Unix/Linux</a> > .vimrc

# .vimrc (Vim Run Command)



## What is a .vimrc File?

* A setting file used by Vim
* Saves the default settings for the Vim editor when it is opened
* Allows users to customize options for the editor



## My .vimrc

```shell
"*******************************************************************************
" @file     : .vimrc (Vim Run Command)
" @brief    : Optimized for C/C++ development, but useful also for other things
" @author   : Kyungjae Lee
" @date     : Jan 14, 2021 - Created file
"*******************************************************************************

"-------------------------------------------------------------------------------
" Development Environment Configuration
"-------------------------------------------------------------------------------

" turn line numbers on
set number

" wrap line at 90 chars 
"set textwidth=90
                                        
" mark the end of the desired textwidth
set colorcolumn=80
highlight ColorColumn ctermbg=DarkGray

" display current column number
set ruler

" highlight all search matches 
set hlsearch

" configure tabwidth and insert spaces instead of tabs 
set tabstop=4
set shiftwidth=4

" use indentation of previous line
set autoindent

" use intelligent indentation for C
set smartindent

" use auto-comment in new line (use ctrl-u in insert mode to delete the 
" automatically inserted comment markers in one key-chord)
set formatoptions=tcroql

" enable syntax color scheme
syntax on

" fixes glitch? in colors when using vim with tmux
set background=dark
set t_Co=256

" underline the current line
"highlight CursorLine cterm=NONE ctermbg=NONE ctermfg=NONE guibg=NONE guifg=NONE
set cursorline

" configure the line number
"highlight LineNr term=bold cterm=NONE ctermfg=DarkGrey ctermbg=NONE gui=NONE guifg=DarkGrey guibg=NONE

" configure comment color 
highlight Comment ctermfg=DarkGreen

" configure statusline
set laststatus=2
" display the full path of the currently open file
set statusline+=%F
" display the line and column numbers left-justified
set statusline+=\ %=L%l:%=C%c
" highlight the statusline                                                                                       
highlight StatusLine ctermfg=Blue

"-------------------------------------------------------------------------------
" Key Bindings
"-------------------------------------------------------------------------------

" personal auto-commenting template                                             
map <F7> o/* <CR>@KyungjaeLee <C-R>=strftime("%m/%d/%Y")<CR><CR>/<ESC><UP><UP>
```
