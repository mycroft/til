# (neo)Vim cheatsheet

## Moving the cursor (aka motion)

- k ~ go up
- j ~ go down
- h ~ go left
- l ~ go right

- e ~ go to end of next word
- w ~ go to start of next word
- $ ~ go to end of line

- <num><motion> - repeat motion num times

- 0 ~ go to start of line

- G ~ go to end of file
- <num>G - go to this line number
- gg ~ go to start of file


## Searching

- /<pattern> - search forward
- ?<pattern> - search backward


## Edition

- x ~ delete current character
- i ~ enter insert mode
- a ~ enter insert mode starting to next char
- A ~ enter insert mode starting to the end of line (ie: appending)

- de ~ delete until end of word, including its last char
- dw ~ delete until start of next word, excluding its 1st char
- d$ ~ delete until end of line
- dd ~ delete current line

- u ~ undo last command
- U ~ undo all changes to current line


## System

- <ESC>:q! ~ leave without saving
- <ESC>:wq! ~ write and leave
- <ESC>:x! ~ write and leave


# Resources

- [vimtutor](https://vimschool.netlify.app/)
