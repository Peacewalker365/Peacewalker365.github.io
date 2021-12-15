# Vim Note


# Termial Oriented Programming







### Some Useful Info



https://vim-adventures.com/

This is a game for vim beginner.



https://www.destroyallsoftware.com/screencasts

This is an amazing guy introducing the power of the terminal.

Doing everything in one terminal window is powerful.



### Jobs Control

jobs

ps

fg

bg

fg %1

kill %2

kill -9 24153

./some_codes.sh &

ctrl + z to suspend the current job including when you are in the Vim

ctrl + c to kill the current job



### Map

:map ,l :!g++ std=c++11 % -Wall -o name<cr>

:map

:map ,l

:! ls -a

:! command1 && command2 means that command 1 has to be suceeded before the command 2 runs

:! c1 || c2 means that no matter c1 runs successfully or not the c2 will run

:map ,l :w<cr> && :!g++ std=c++11 % -Wall -o name && name<cr> This is actually not a good practice since you may want to manually control when you save the file

:vs

:map <c-l> <c-w><c-l> move to the right vim session

:! echo % prints out the current file name

:!echo %:r prints out the current file name without extension, actually it just remove the very last extension, but you can use it multiple times.

:!echo %:h header, it will give you just a . since the current folder is presented as .

:!echo %:p:h this will give you the current folder's full path

:!echo %:p this is full path

:!echo %:~ this is relative path to home, will stay unmodified if it's not under home dir

:!echo %:. reduced path e.g. test.txt

:!mv currentPath newPath the way to rename a file or maybe add a plugin

:map .. :e %:r_spec.rb<cr> it lets you jump to corresponding spec file

And we also have many other map ways

:nm[ap]

:vm[ap]

:xm[ap]

and etc.

which let you map the keys for diff modes

:unmap .l

:noremap lhs rhs means the map cannot be mapped recursively

:remap works the oppsite

:!! repeat the last one

:ter Opens a terminal

### Commands

#### verbs

mm => mark here as m

`m => go back to the mark m

d => cut 
dw means delete a word

c => change (delete and enter the insert mode)
> => indent
v => visually select
y => Yank (copy)

u => undo
ctrl+r => redo

. => repeat the last operation

#### motions
w => word (forward by a "word")
b => back (back by a "word")
2j => down 2 lines

yy => copy the entire lines


#### text object
iw => "inner word" (works from anywhere in a word)
it => "inner tag" (the contents of an HTML tag)
i" => "inner quotes"
ip => "inner paragraph"
as => "a sentence"

#### Nouns --Parameterized Text Objects
f => find the next character
F reverse the direction
t => find the next char but not include that char
T reverse
/ search the next match
? reverse

#### Combinations of Commands
combination of these commands are already powerful
and there are more to explore

#### Relative Numbers
:set relativenumber => this is so powerful!!

#### Visual Mode is a Smell
visual breaks repatability! be mindful of this, don;t use it unless you have to

#### Custom Operators (https://www.youtube.com/watch?v=wlR5gYd6um0)
Surround
Commentary
ReplaceWithRegister
Titlecase
Sort-motion
System-copy

#### Custom Text Objects
Indent 
Entire
Line 
Ruby block




